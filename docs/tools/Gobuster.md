### 1) What Gobuster actually does (and why)

Gobuster is a **wordlist-driven enumerator**. It takes a base target (URL, domain, or IP) and **systematically tries candidates** from a wordlist, then **classifies responses** (by status code, size, redirect, etc.). That makes it ideal for:

- **Hidden content discovery** on web servers (`dir` mode)
    
- **Finding subdomains** that DNS knows about (`dns` mode)
    
- **Discovering name-based virtual hosts** behind a single IP (`vhost` mode)
    

It sits **between reconnaissance and scanning**: you already have a target, and now you’re enumerating “what else is there?”

---

### 2) Wordlists: the beating heart

Choose wordlists that fit the job. A few practical pointers:

- **Start small**, confirm signal, then scale up.
    
- **Tech-aware lists** help (e.g., WordPress, admin panels, API routes).
    
- **Extensions matter**: add `.php,.asp,.aspx,.jsp,.js,.json,.bak,.old,.zip` etc. based on stack clues.
    
- **For DNS/vhosts**, use high-quality subdomain lists. Trim ultra-generic entries if wildcard noise is high.
    

If you have **no clue**, begin with:

- `directory-list-2.3-small.txt` → sanity check
    
- `directory-list-2.3-medium.txt` → broader sweep
    
- `subdomains-top1million-5000.txt` → fast subdomain baseline
    

---

### 3) Response interpretation (critical skill)

Gobuster is only as good as your interpretation:

- **200 OK**: Exists (access may be restricted by auth on the page itself).
    
- **301/302 Redirect**: Exists; redirected elsewhere (follow with `-r` if you need final location).
    
- **401 Unauthorized / 403 Forbidden**: Exists but blocked—**gold for pentests** (tells you what’s worth digging into).
    
- **404 Not Found**: Usually not found, but beware apps that **return 200/302 for everything**; then filtering is needed.
    
- **Size patterns**: Uniform sizes across many “found” paths often signal **false positives** (error page or wildcard). Use filtering (`--exclude-length`), status code filters (`-s`/`-b`), or adjust logic.
    

---

### 4) `dir` mode (directories & files)

##### Core syntax

```bash
gobuster dir -u http://target.tld -w /path/to/wordlist
```

##### Why each flag matters

- `-u` requires **protocol** (`http://` or `https://`). Missing/incorrect protocol breaks requests.
    
- `-w` is mandatory; every guess comes from here.
    
- `-x` adds **file extensions** (Gobuster tries both `/path` and `/path.ext`).
    
- `-t` increases concurrency (balance speed vs. detection risk).
    
- `-r` follows redirects; useful when apps shuffle you around.
    
- `-k` ignores TLS errors against self-signed lab certs.
    

##### Solid examples

Enumerate directories and common script files:

```bash
gobuster dir -u https://shop.example.tld \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -x .php,.js,.json -t 50 -r -o gobuster-dir.txt
```

Target technology-specific areas (WordPress):

```bash
gobuster dir -u http://blog.example.tld \
  -w /usr/share/wordlists/dirb/small.txt \
  -x .php \
  -s 200,204,301,302,307,401,403
```

Authenticated scans (carry your session cookie):

```bash
gobuster dir -u https://portal.example.tld \
  -w /usr/share/wordlists/dirb/common.txt \
  -c "PHPSESSID=abcd1234; logged_in=true" \
  -H "User-Agent: Mozilla/5.0 Pentest"
```

##### Key realities

- **Not recursive**: If `/admin/` is found, **rerun** Gobuster with `-u https://site/admin/` to dig deeper.
    
- **Signal boosting**: Try **method variations** server-side (e.g., if allowed via app logic), or extend extensions list.
    
- **Noise control**: Use `-s` or `-b` to include/exclude status ranges; combine with `--exclude-length` if error pages cause same-size false positives.
    

---

### 5) `dns` mode (subdomains)

##### Core syntax

```bash
gobuster dns -d example.tld -w /path/to/subdomains.txt
```

##### Helpful flags

- `-i` shows **resolved IPs** (quick mapping of estate).
    
- `-c` shows **CNAMEs** (often reveals underlying SaaS or old infrastructure).
    
- `-r` sets a **custom resolver** (e.g., internal DNS or a trusted external one).
    

##### Example

```bash
gobuster dns -d example.tld \
  -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \
  -i -c -o gobuster-dns.txt
```

##### Wildcard DNS caution

Some domains resolve **any** `*.example.tld` to a default IP (catch-all). Symptoms:

- Nearly every guess appears “Found”.
    
- CNAME or A records are identical for random nonsense.
    

Mitigations:

- Validate a few random false positives with `dig`/`nslookup`.
    
- Filter on **status/size** later with HTTP probing (combine `dns` + `vhost/dir` workflows).
    
- Use a **resolver** you trust with `-r`.
    

---

### 6) `vhost` mode (virtual hosts)

Name-based virtual hosting means many hostnames share one IP. You **must** hit the right Host header.

##### Core syntax

```bash
gobuster vhost -u http://<IP-or-base-host> -w /path/to/subdomains.txt
```

##### Key flags (practical)

- `--domain example.tld` ensures the `Host:` header follows `word.example.tld`.
    
- `--append-domain` appends the domain to each wordlist entry.
    
- `--exclude-length` filters **same-size** false positives from default vhost pages.
    
- `-r` follows redirects, which often land on the “main” site.
    

##### Example

```bash
gobuster vhost -u http://10.10.10.10 \
  --domain example.tld \
  --append-domain \
  -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \
  --exclude-length 240-320 \
  -o gobuster-vhost.txt
```

##### Why `vhost` is different from `dns`

- `dns` only asks **DNS** “does this name exist?”
    
- `vhost` makes **HTTP requests** with different `Host:` values to the **same IP** to see which names are actually **served**.
    

---

### 7) `fuzz` mode (keyword-based fuzzing)

Lets you replace the token **`FUZZ`** in URL, headers, or body.

##### Examples

Fuzz a path segment:

```bash
gobuster fuzz -u 'https://api.example.tld/FUZZ' \
  -w /usr/share/wordlists/dirb/common.txt
```

Fuzz a header (e.g., API keys from a small candidate list):

```bash
gobuster fuzz -u 'https://api.example.tld/resource' \
  -H 'X-API-Key: FUZZ' \
  -w ./keys.txt -t 30
```

Fuzz POST body (simple parameter wordlist):

```bash
gobuster fuzz -u 'https://app.example.tld/login' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -X POST \
  -w ./usernames.txt \
  --pattern 'username=FUZZ&password=test'
```

When fuzzing, **interpret by status codes and sizes**; build allow/deny lists with `-s`/`-b` and `--exclude-length`.

---

### 8) Cloud buckets (`s3` and `gcs`)

These modes try bucket names from a wordlist.

##### S3 example

```bash
gobuster s3 -w ./bucket-names.txt -o gobuster-s3.txt
```

##### GCS example

```bash
gobuster gcs -w ./bucket-names.txt -o gobuster-gcs.txt
```

Interpretation:

- “Found” doesn’t always mean **publicly listable**—you might only confirm existence. Test ACLs manually and check for world-readable objects or misconfigurations.
    

---

### 9) Performance and stealth tuning

- **Threads (`-t`)**: 20–100 is common in labs; real targets may need **lower** to avoid rate-limits/WAF.
    
- **Delay (`--delay 500ms`)**: Adds jitter to look less robotic.
    
- **Headers and UA (`-H`, custom UA)**: Some WAFs rate-limit default tool UAs.
    
- **Timeouts**: Defaults are usually fine; very slow servers may need tuning.
    
- **Output (`-o`)**: Always save results. You will revisit and re-scope.
    

Resuming long runs:

```bash
gobuster dir -u https://site -w biglist.txt --wordlist-offset 250000
```

Stdin wordlists (dynamic pipelines):

```bash
cat candidates.txt | gobuster dir -u https://site -w -
```

---

### 10) Typical pentest workflow

1. **Recon**
    
    - Identify technology hints (headers, error pages, `/robots.txt`, JS).
        
    - Note likely extensions and directories.
        
2. **dir baseline**
    
    - Small list at `/` to validate signal.
        
    - Expand with medium list + extensions.
        
    - Re-target promising hits recursively (manually).
        
3. **dns sweep**
    
    - Enumerate subdomains; resolve to IPs; cluster infra by IP/CNAME.
        
4. **vhost sweep**
    
    - For interesting IPs, run `vhost` to find additional sites on the same box.
        
    - Filter by response length/status to drop default vhost noise.
        
5. **prioritize**
    
    - 200/30x/401/403 paths and vhosts go to the top of manual testing.
        
    - Spider app flows, look for auth boundaries, admin panels, backups, config leaks.
        
6. **fuzz surgically**
    
    - If param names/keys/verbs are uncertain, fuzz constrained spaces with `fuzz` mode.
        

---

### 11) Troubleshooting & false positives

- **“invalid value for flag -d” in `dns` mode**
    
    - Ensure `-d` gets **only a domain** (no protocol, no slashes): `-d example.tld`, not `http://example.tld`.
        
- **Nothing shows up in `dir`**
    
    - Wrong protocol? Try the other scheme.
        
    - App uses **catch-all** handlers returning 200 for everything—filter with `-b` or `--exclude-length`, or target **known extensions**.
        
- **Uniform 404/200 sizes**
    
    - Use `--exclude-length` with a single size or a range.
        
    - Try `-s 200,204,301,302,307,401,403` to keep only meaningful codes.
        
- **Self-signed HTTPS**
    
    - Add `-k`.
        
- **Wildcard DNS**
    
    - Expect noise; validate with second tool; probe with `vhost`/HTTP to confirm real apps.
        
- **Target is rate-limiting**
    
    - Lower `-t`, add `--delay`, rotate headers.
        

---

### 12) Practical, targeted examples

##### A) API with JSON routes

```bash
gobuster dir -u https://api.example.tld \
  -w /usr/share/wordlists/dirb/common.txt \
  -x .json \
  -s 200,204,301,302,307,401,403 \
  -t 40 -o api-routes.txt
```

Rationale: many APIs expose `/.well-known`, `/v1/`, `/health`, `/metrics`, `/swagger.json`.

##### B) WordPress target (themes/plugins/uploads)

```bash
gobuster dir -u http://blog.example.tld \
  -w /usr/share/wordlists/dirb/small.txt \
  -x .php \
  -s 200,301,401,403 \
  -t 50
# Gobuster
gobuster dir -u http://blog.example.tld/wp-content/ \
  -w /usr/share/wordlists/dirb/common.txt \
  -s 200,301,401,403
```

Rationale: manually recurse into `wp-content/plugins/` and `uploads/` where leaks often live.

##### C) Find shadow vhosts on a shared IP

```bash
gobuster vhost -u http://203.0.113.10 \
  --domain example.tld \
  --append-domain \
  -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \
  --exclude-length 240-320 -t 50
```

Rationale: filters out identical default pages, surfaces real properties.

---

### 13) Hygiene and ethics

- Get **authorization** in writing; follow scope.
    
- Keep concurrency safe for the environment.
    
- Log artifacts and timestamps; store Gobuster outputs with context for later reporting.
    

---

### 14) Quick reference (field cheat sheet)

- **dir (fast baseline)**
    

```bash
gobuster dir -u https://site -w /.../directory-list-2.3-small.txt -t 50 -s 200,301,401,403
```

- **dir (with extensions)**
    

```bash
gobuster dir -u https://site -w /.../directory-list-2.3-medium.txt -x .php,.js,.json -r -t 60
```

- **dns (IPs + CNAME)**
    

```bash
gobuster dns -d example.tld -w /.../subdomains-top1million-5000.txt -i -c
```

- **vhost (shared IP)**
    

```bash
gobuster vhost -u http://IP --domain example.tld --append-domain -w /.../subdomains.txt --exclude-length 240-320
```

- **fuzz (header or path)**
    

```bash
gobuster fuzz -u 'https://api.example.tld/FUZZ' -w ./candidates.txt
gobuster fuzz -u 'https://api.example.tld' -H 'X-API-Key: FUZZ' -w ./keys.txt
```

---