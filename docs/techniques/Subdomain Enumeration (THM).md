# Subdomain Enumeration (THM)

**What it is:**  
Public, append-only logs of every SSL/TLS certificate issued by CAs. Meant to make issuance public so rogue/accidental certificates are noticed quickly.

**Why useful for subdomain discovery:**  
A certificate may be issued for `sub.example.com` even if DNS isn’t publicly listing it anywhere else. CT logs (and websites that index them) let you find historical and current certificates that include subdomain names.

**How to use (example with crt.sh):**

1. Open `https://crt.sh`.
    
2. Search for `example.com` (or any domain).
    
3. Filter or sort by **Logged At** date to find certificates logged on a specific date (e.g., `2020-12-26`).
    
4. Each cert record will show Subject/CN and SANs — extract subdomain names from those SANs.
    

**Example (conceptual):**

- Search result row (Logged At: `2020-12-26`) → SAN: `svc1.example.com` → `svc1.example.com` is a candidate subdomain.
    

**Tips:**

- Use the crt.sh advanced query: `%.example.com` or `example.com` and then sort/filter by logged date.
    
- Download results / copy SANs and deduplicate.
    
- Watch out for wildcard certs (e.g., `*.example.com`) — they indicate many subdomains but don’t list them explicitly.
    

---

# Google site: searches for subdomains (passive discovery)

**What it is:**  
Search engines index pages that often reveal subdomain names in links or titles (e.g., `blog.example.com`).

**Useful search pattern:**

```
site:*.example.com -site:www.example.com
```

This returns pages on subdomains of `example.com` but excludes `www.example.com`.

**How to use:**

- Run that query in Google (or Bing with similar syntax).
    
- Look through URLs in results: any `subdomain.example.com` present is a candidate.
    

**Example result (conceptual):**

- `subdomain.example.com/path/to/resource` → discovered `subdomain.example.com`.
    

**Tips:**

- Combine with keywords like `site:*.example.com inurl:admin` to find admin panels on subdomains.
    
- Use `cache:` and `intext:` to find references to internal names.
    

---

# DNS brute-force and Host Header fuzzing (active discovery)

**What it is:**

- **DNS brute-force:** try many guessed subdomain names and check DNS resolution.
    
- **Host header fuzzing (virtual host discovery):** send HTTP requests to a single IP but set the `Host:` header to different names (the server will reply differently if the host header maps to a hosted virtual site).
    

**Why host header fuzzing:**  
Many servers host multiple websites on one IP (virtual hosts). DNS may not reveal subdomains (private DNS or hosts file). If the webserver is configured for a host header, the request may return a unique page for that host.

---

# [[FFUF]] Host header fuzzing walkthrough (your AttackBox exercise)

**Given command:**

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt
     -H "Host: FUZZ.example.com"
     -u http://10.201.125.226
```

- `-w` wordlist (FUZZ tokens are replaced by each word)
    
- `-H` add or edit header; here we add `Host: <candidate>`
    
- `-u` URL to request (the server IP)
    

**Why that always produces results:**  
All requests hit the same IP and the webserver always returns some content (so you'll get many responses). You need to filter out the “normal” default response and keep anomalies (the pages that differ in size, status code, or words).

### Step 1 — Run ffuf to see all results

Run the basic command and inspect the “Size” column or status codes in the output.

### Step 2 — Find the most common page size (the default page)

The **most common Size** value corresponds to the default response (e.g., the server’s catch-all page). Use that size with `-fs` to filter it out.

**How to get that size quickly (manual method):**

1. Run `ffuf` interactively and look at the printed `Size` column — pick the size that appears most often.
    
2. Or write output to JSON and compute the modal size:
    

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt 
     -H "Host: FUZZ.example.com" 
     -u http://10.201.125.226 
     -of json -o ffuf_out.json
# then parse ffuf_out.json with jq/python to find most common "content_length"
```

**Simpler CLI parse (if ffuf prints to stdout):**

```bash
ffuf -w ... -H "Host: FUZZ.example.com" -u http://10.201.125.226 | awk '{print $6}' | sort | uniq -c | sort -nr | head
```

_(adjust column index depending on ffuf version/format — the goal is to count the Size column values.)_

### Step 3 — Re-run filtering out the common size

If the most frequent size was `1234`, run:

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt 
     -H "Host: FUZZ.example.com" 
     -u http://10.201.125.226 
     -fs 1234
```

This will **ignore** results that return a response body of size `1234` bytes, leaving results that differ (possible virtual hosts).

### Example expected outcome (illustrative)

After filtering you might see two hits:

```
admin   | 200 |  5123 | admin.example.com
dev     | 200 |  4602 | dev.example.com
```

These indicate `admin.example.com` and `dev.example.com` responded with pages different from the default.

### Verify each candidate

Always verify by sending a request with curl or by visiting in a browser (if allowed):

```bash
curl -I -H "Host: admin.example.com" http://10.201.125.226
# or full request to inspect body
curl -H "Host: admin.example.com" http://10.201.125.226 | head -n 50
```

Look at `Server`, `Location`, or login forms in the response body.

---

# Validating & triaging results

- **Status code check**: prefer `200`, `301`/`302` (redirects) or any non-default status code.
    
- **Headers**: `Server`, `X-Powered-By`, `Set-Cookie` may indicate a real application.
    
- **Content checks**: use `-mc` (match codes) or `-fr` (filter regex) in ffuf to find pages with certain keywords (e.g., `admin`, `login`).
    
- **Manual inspection**: always eyeball the page for login panels, unique banners, response differences.
    

---

# Example end-to-end ffuf workflow (compact)

1. Run ffuf:
    

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt 
     -H "Host: FUZZ.example.com" 
     -u http://10.201.125.226 
     -o ffuf_raw.txt
```

2. Determine common size (from `ffuf_raw.txt`) and set `-fs`:
    

```bash
# (rough parsing)
most_common_size=$(awk '{print $6}' ffuf_raw.txt | sort | uniq -c | sort -nr | head -n1 | awk '{print $2}')
ffuf -w ... -H "Host: FUZZ.example.com" -u http://10.201.125.226 -fs $most_common_size -o ffuf_filtered.txt
```

3. Verify findings:
    

```bash
while read -r line; do host=$(echo $line | awk '{print $4}'); curl -I -H "Host: $host" http://10.201.125.226; done < ffuf_filtered.txt
```

_(Adjust parsing depending on ffuf output format.)_

---

# Additional techniques & tools

**Passive enumeration**

- `crt.sh`, `Censys`, `Shodan`, `VirusTotal`, `SecurityTrails`, `Common Crawl`, Wayback Machine.
    
- `subfinder`, `assetfinder`, `Amass` (passive mode) — great to gather names quickly.
    

**Active enumeration**

- `amass enum -active` (DNS bruteforce + scraping)
    
- `massdns` for high-speed resolution
    
- `dnsenum`, `dnsrecon` for brute force and zone transfer checks
    
- `subbrute` / `subfinder` + wordlists (SecLists)
    

**Certificate-specific**

- Use CT APIs: `crt.sh/?q=%.example.com&output=json` to pull JSON results programmatically.
    
- Use `certstream` (live stream) for real-time certificate monitoring.
    

**Wordlists**

- SecLists: `Discovery/DNS/namelist.txt`, `Discovery/DNS/subdomains-top1million-5000.txt`.
    
- Create custom lists from GitHub, code repos, or wayback historical subdomains.
    

**Handling wildcard certs**

- Wildcard certs show `*.example.com` — use other discovery methods (passive + brute force) to enumerate explicit subdomains.
    

**Host header tricks**

- Try `-H "Host: nonexistent.example.com"` to detect default server behavior.
    
- Check for host header takeovers (misconfigured DNS + PaaS services) — only on assets you are authorized to test.
    

**Filtering strategies in ffuf**

- `-mc` to match status codes (e.g., `-mc 200,301`)
    
- `-fc` to filter out status codes (e.g., `-fc 404`)
    
- `-fr` to filter by regex in the response body
    
- `-fs` to filter by size (your scenario)
    

**Automation & pipelines**

- Combine `subfinder`/`amass` → `massdns` → `httpx` → `ffuf` → `nuclei` for content-based discovery & vuln scanning (authorized only).
    
- Use `jq` and `grep` to de-duplicate and parse lists.
    

---
### DNS zone transfer (AXFR)

**What it is:**  
A DNS zone transfer (`AXFR`) is a mechanism for copying the DNS zone file from a primary DNS server to a secondary. If misconfigured, it can leak all hostnames for a domain.

**Why useful:**  
A successful AXFR returns a complete list of DNS records (subdomains, MX, TXT, etc.) without brute forcing.

**How to try (example):**

```bash
dig AXFR @ns1.example.com example.com
# or using host
host -l example.com ns1.example.com
```

**Tool:** `dig`, `host`, `dnsrecon` (`dnsrecon -d example.com -t axfr`)

**Notes:** Rare on modern setups but high-value when present.

---

### Reverse DNS (PTR) sweep & IP-to-host mapping

**What it is:**  
PTR records map IPs to hostnames. Scanning IP ranges and doing reverse lookups can reveal hostnames/subdomains.

**Why useful:**  
Identifies hostnames associated with infrastructure IP ranges (CDNs, cloud VMs).

**Example tools/commands:**

- `massdns` + list of IPs → reverse resolution
    
- `nmap --script=http-hostheader --script-args ...` or simple reverse `dig -x IP`
    

**Tip:** Combine with ASN ownership lookup to focus on the target provider range.

---

### High-speed discovery: `masscan`, `naabu`, `massdns`

**What they do:**

- `masscan` — super-fast port scanner (like `nmap` but faster).
    
- `naabu` — fast port-probing for many hosts.
    
- `massdns` — designed for bulk DNS resolution (very fast).
    

**Why useful:**  
Resolve and probe millions of names quickly; find hosts that actually respond on HTTP/HTTPS.

**Example pipeline:**

1. Generate candidate subdomains (wordlist).
    
2. Use `massdns` to resolve them quickly.
    
3. Feed resolved IPs to `naabu`/`httpx` to check HTTP services.
    

**Example commands (conceptual):**

```bash
massdns -r resolvers.txt -t A -o S -w resolved.txt candidates.txt
httpx -l resolved.txt -o alive.txt
```

---

### HTTP probing & content sniffing: `httpx`, `httprobe`, `curl`

**What they do:**

- `httpx` — probe hosts for HTTP/HTTPS, gather title, status code, TLS info.
    
- `httprobe` — similar lightweight tool.
    

**Why useful:**  
Quickly filter resolved hosts to those serving web content, avoid wasted web fuzzing.

**Example:**

```bash
httpx -l targets.txt -status-code -title -o web_alive.txt
```

---

### Web crawling & link scraping: `gau`, `waybackurls`, `hakrawler`, `gospider`

**What they do:**  
Collect historical and current URLs for a domain (Wayback/Archive + live crawling).

**Why useful:**  
Find hidden paths, subdomains in JavaScript, endpoints, API hosts, or internal names.

**Examples:**

```bash
# Get URLs from Wayback + common sources
gau example.com | cut -d/ -f3 | sort -u
waybackurls tryhackme.com > wayback_urls.txt
hakrawler -url https://sub.example.com
```

**Tip:** Pipe results into `gf` / `ffuf` / `httpx` for further triage.

---

### FQDN permutation & mutation: `dnsgen`, `altdns`

**What they do:**  
Generate permutations, typos, and variations of discovered subdomains automatically.

**Why useful:**  
Catches staging/dev/test hostnames that are naming variants of known names (e.g., `dev-portal`, `portal-dev`).

**Example:**

```bash
altdns -i subdomains.txt -o permutations.txt -w wordlist.txt
dnsgen subdomains.txt wordlists/words.txt > mutated.txt
```

---

### Subdomain takeover checks & tools

**What it is:**  
A takeover occurs when a DNS entry points to an unclaimed service (e.g., an S3 bucket or GitHub Pages without content). Attackers can claim it and host content.

**Tools:** `subjack`, `takeover` (rising), `tko-subs`

**Example usage (conceptual):**

```bash
subjack -w subdomains.txt -t 50 -timeout 30 -o vulnerable.txt -ss
```

**Safety:** Only check takeovers on assets you are authorized to test; responsible disclosure policy applies.

---

### Certificate APIs & real-time monitoring

**What they do:**

- `crt.sh` JSON output: `https://crt.sh/?q=%.example.com&output=json`
    
- `Certstream` — stream certificates in real time.
    

**Why useful:**  
Programmatically pull CT entries and watch for new certificates issued for a target domain.

**Example (curl):**

```bash
curl "https://crt.sh/?q=%.tryhackme.com&output=json" | jq '.[].name_value' | sort -u
```

---

### Code-repository and leaked-secret scanning: GitHub Dorks & `truffleHog`, `gitleaks`

**What they do:**  
Search GitHub for domain references, subdomains, credentials; scan for secrets in commits.

**Why useful:**  
Developers sometimes push config files or hosts lists containing internal hostnames/subdomains.

**Example GitHub search:**

```
site:github.com "example.com" "localhost" "config"
```

**Tools:** `gitleaks`, `truffleHog`, `repo-supervisor`.

---

### Cloud asset & metadata discovery (AWS/Azure/GCP)

**What it is:**  
Cloud-hosted services (S3 buckets, Azure Blob, CloudFront, GCP storage) often use predictable hostnames that include subdomain-like names.

**Tools:** `cloudmapper`, `cloud_enum` (defensive), `cloudflare-resolve`, `subfinder` with cloud sources.

**Why useful:**  
Find storage buckets, bucket hostnames, and cloud services tied to the organization.

**Example checks:** Look for `*.s3.amazonaws.com`, `*.azurewebsites.net`, `*.cloudfront.net`.

---

### Automation pipelines & orchestration example

**Purpose:** Automate repeated steps into a reliable pipeline.

**Example pipeline (one-liner concept):**

```bash
subfinder -d example.com -o passive.txt
amass enum -d example.com -o amass.txt
cat passive.txt amass.txt | sort -u > all_candidates.txt
massdns -r resolvers.txt -t A -o S -w resolved.txt all_candidates.txt
httpx -l resolved.txt -o web_alive.txt
ffuf -w sublist.txt -H "Host: FUZZ.example.com" -u http://<ip> -fs <size>
```

**Why useful:** Faster, consistent results and easy to schedule.

---

### Content discovery & vuln scanning: `nuclei`, `ffuf` (advanced), `dirsearch`

**What they do:**

- `nuclei` — fast templates for scanning for known vuln patterns (CVE, misconfigs).
    
- `dirsearch` / `gobuster` — directory and file bruteforce on web servers.
    
- `ffuf` advanced flags: `-mc`, `-fc`, `-fr`, `-ml` (match lines), `-ma` (match all)
    

**Example:**

```bash
nuclei -l web_alive.txt -t cves/ -o nuclei_results.txt
gobuster dir -u https://sub.example.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

---

### Rate-limiting, proxies, and stealthy enumeration

**What it is:**  
Tuning concurrency and using proxies/resolvers to avoid triggering WAFs/IDS or exhausting public resolvers.

**Why useful:**  
Prevents IP blocks and keeps scanning within acceptable bounds.

**Best practices:**

- Respect `robots.txt` where applicable (though it’s not security policy).
    
- Use `--rate`/`-t` to throttle (`ffuf -t 10`).
    
- Use `burp` or `tor`/proxy pools for higher anonymity (be aware of TOS/legal aspects).
    
- Use your own resolvers or reputable public resolvers and keep query rates reasonable.
    

---

### Logging, reporting & reproducibility

**What to do:**

- Save raw outputs (`-o`/`-of` flags) and JSON exports.
    
- Use `jq`/`pandas` for parsing and deduping.
    
- Create a small report that lists: discovered subdomain, resolved IP(s), HTTP status, title, notes, date/time, evidence (curl output).
    

**Why useful:**  
Auditable, repeatable results for later verification or handoff.

---
### Exporting, reporting & next steps

**Export to PDF (manual steps):**

1. Copy the Results to a local Markdown file and use `pandoc`:
    

```bash
pandoc results.md -o subdomain_discovery.pdf
```

---

## Automation Script: `discover_subdomains.sh`

**Purpose:** Demonstration of a safe, repeatable pipeline that:

1. Runs passive collection (`subfinder`).
    
2. Resolves candidates with `massdns`.
    
3. Probes for web servers using `httpx`.
    
4. Performs ffuf host-header fuzzing against a target IP, detects most common response size, re-runs ffuf with `-fs` to filter the default page, and verifies candidates with `curl`.
    

**Requirements:** `subfinder`, `massdns`, `httpx`, `ffuf`, `jq`, `awk`, `sort`, `uniq`, `curl` installed and in PATH. A `resolvers.txt` file for `massdns` (list of DNS resolvers).

**Usage:**

```bash
sudo ./discover_subdomains.sh DOMAIN TARGET_IP 
# example: sudo ./discover_subdomains.sh tryhackme.com 10.201.125.226
```

**Script content:**

```bash
#!/usr/bin/env bash
set -euo pipefail

if [[ $EUID -ne 0 ]]; then
  echo "Warning: some tools (massdns) may require root privileges. Proceeding without sudo may still work."
fi

if [ "$#" -ne 2 ]; then
  echo "Usage: $0 <domain> <target_ip>"
  exit 1
fi

DOMAIN="$1"
TARGET_IP="$2"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
WORKDIR="./discovery_${DOMAIN}_${TIMESTAMP}"
WORDLIST="/usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt"
RESOLVERS="resolvers.txt"  # Provide your list of resolvers

mkdir -p "$WORKDIR"
cd "$WORKDIR"

echo "[*] Running passive discovery with subfinder..."
subfinder -d "$DOMAIN" -silent -o subfinder.txt || true

# optional: amass passive merge
if command -v amass >/dev/null 2>&1; then
  echo "[*] Running amass (passive)..."
  amass enum -passive -d "$DOMAIN" -o amass.txt || true
fi

# combine and dedupe
cat subfinder.txt amass.txt 2>/dev/null || true
cat subfinder.txt amass.txt 2>/dev/null | sort -u > candidates.txt || true

# fallback if no combined file
if [ ! -s candidates.txt ]; then
  if [ -s subfinder.txt ]; then cp subfinder.txt candidates.txt; fi
  if [ -s amass.txt ]; then cp amass.txt candidates.txt; fi
fi

sort -u candidates.txt -o candidates.txt || true

echo "[*] Resolving candidates with massdns (requires resolvers.txt) ..."
if [ ! -f "$RESOLVERS" ]; then
  echo "Provide a resolvers.txt file in $PWD with one resolver per line (e.g. 1.1.1.1). Exiting."
  exit 1
fi

if command -v massdns >/dev/null 2>&1; then
  massdns -r "$RESOLVERS" -t A -o S -w massdns_out.txt candidates.txt || true
  # massdns S output: "example.com. A 1.2.3.4"
  awk '/ A /{print $1}' massdns_out.txt | sed 's/\.$//' | sort -u > resolved_names.txt || true
else
  echo "massdns not found; skipping bulk resolve. Using candidates as resolved names."
  cp candidates.txt resolved_names.txt
fi

# probe for HTTP services
if command -v httpx >/dev/null 2>&1; then
  httpx -l resolved_names.txt -silent -ports 80,443 -o web_alive.txt || true
  # httpx output may be host:port or url; normalize to hostnames
  awk -F/ '{print $3}' web_alive.txt | sed 's/:.*$//' | sort -u > web_hosts.txt || true
else
  echo "httpx not found; creating web_hosts.txt from resolved_names.txt"
  cp resolved_names.txt web_hosts.txt
fi

# If web_hosts.txt is empty, fallback to candidates
if [ ! -s web_hosts.txt ]; then
  echo "No web hosts found; falling back to resolved_names.txt"
  cp resolved_names.txt web_hosts.txt
fi

# Run ffuf host header fuzzing against the target IP using top wordlist and web_hosts as base
FFUF_OUT_RAW="ffuf_raw.txt"
FFUF_OUT_FILTERED="ffuf_filtered.csv"

echo "[*] Running ffuf (first pass) against $TARGET_IP ..."
# safer defaults: limit threads (-t) to reasonable concurrency to avoid overloading
ffuf -w "$WORDLIST" -H "Host: FUZZ.$DOMAIN" -u "http://$TARGET_IP" -t 40 -o "$FFUF_OUT_RAW" || true

# try to parse JSON/CSV output if ffuf wrote it; otherwise parse stdout raw output
modal_size=""
if [ -f "$FFUF_OUT_RAW" ]; then
  if grep -q "Size:" "$FFUF_OUT_RAW" 2>/dev/null; then
    modal_size=$(grep -oP 'Size: \K[0-9]+' "$FFUF_OUT_RAW" | sort -n | uniq -c | sort -rn | head -n1 | awk '{print $2}') || true
  fi
fi

if [ -z "$modal_size" ]; then
  echo "[!] Could not auto-detect modal size from ffuf output. Please inspect $FFUF_OUT_RAW and set -fs manually if needed."
else
  echo "[*] Most common response size detected: $modal_size"
  echo "[*] Re-running ffuf filtering out default size ($modal_size) ..."
  ffuf -w "$WORDLIST" -H "Host: FUZZ.$DOMAIN" -u "http://$TARGET_IP" -fs "$modal_size" -of csv -o "$FFUF_OUT_FILTERED" -t 40 || true
  # CSV output lines contain: "Input,Status,Size,Words,Lines,URL"
  if [ -f "$FFUF_OUT_FILTERED" ]; then
    awk -F, 'NR>1{print $1}' "$FFUF_OUT_FILTERED" | sed 's/.*FUZZ\.//' | sort -u > ffuf_hits.txt || true
  fi
fi

# Verify hits with curl
if [ -s ffuf_hits.txt ]; then
  echo "[*] Verifying hits with curl..."
  > verification.txt
  while read -r host; do
    echo "== $host ==" >> verification.txt
    curl -s -I -H "Host: $host" "http://$TARGET_IP" | sed -n '1,20p' >> verification.txt
    echo "" >> verification.txt
  done < ffuf_hits.txt
  echo "Verification saved to verification.txt"
else
  echo "No ffuf hits found (ffuf_hits.txt empty)."
fi

echo "[*] Done. Results are in:"
ls -lah

```

---

### Quick-reference tool cheat-sheet

- Passive: `crt.sh` (JSON), `Censys`, `Shodan`, `SecurityTrails`, `subfinder`, `assetfinder`, `amass -passive`.
    
- Active DNS: `massdns`, `dnsrecon`, `dnsenum`, `amass enum -active`.
    
- Host probing: `httpx`, `httprobe`, `naabu`, `masscan`.
    
- Wordlist & mutation: `SecLists`, `altdns`, `dnsgen`.
    
- Web crawling & URLs: `gau`, `waybackurls`, `hakrawler`.
    
- Takeover detection: `subjack`, `tko-subs`.
    
- Fast brute/fuzz: `ffuf`, `gobuster`, `dirsearch`.
    
- Automation/orchestration: combine with `jq`, `xargs`, `parallel`, `Makefile` or a simple script.
    

---
# Legal & ethical considerations

- **Whitelist/permission:** Always get authorization (scope + written permission). For TryHackMe exercises, use the provided lab only.
    
- **Avoid destructive tests** (no brute-force login attempts, no data-exfiltration).
    
- **Respect rate-limits** of services like GitHub, crt.sh, Censys.
    
---
