# SSRF (Server-Side Request Forgery) (THM)

**Server-Side Request Forgery (SSRF)** is a vulnerability where an attacker can make the _server_ issue HTTP(S) (or other protocol) requests to an arbitrary resource chosen by the attacker. The server does the fetching on the attacker’s behalf — which can allow access to internal-only services, cloud metadata endpoints, or other sensitive resources the attacker cannot reach directly.

**Types**

- **Reflected/Visible SSRF:** server returns the fetched response (or part of it) back to the attacker.
    
- **Blind SSRF:** server performs the request but does not return the response; attacker needs an out-of-band listener to observe the request.
    

---

# Why SSRF is dangerous (impact)

- Read internal-only endpoints: `http://127.0.0.1:xxxx`, `http://internal.service/…`
    
- Read cloud provider metadata endpoints (e.g. `169.254.169.254`) → credentials, tokens
    
- Exfiltrate secrets, API keys, config files
    
- Pivot into internal network services not exposed to the internet
    

---

# Common places to look for SSRF

- URL parameters: `?url=…`, `?img=…`, `?server=…`
    
- Hidden form fields: `<input type="hidden" name="src" value="...">`
    
- File-upload / image preview endpoints (that fetch remote images)
    
- Redirect endpoints (open redirects can be chained)
    

---

# Defences & common bypasses (quick)

- **Deny-list** (block `localhost`, `127.0.0.1`) — bypass with alternate encodings (`2130706433`), octal, hex, or DNS entries that resolve to internal IPs (e.g. `127.0.0.1.nip.io`).
    
- **Allow-list** (require prefix `https://website.thm`) — bypass via crafted subdomains: `https://website.thm.attacker.com` (if only prefix-checked).
    
- **Open redirect** — use an allowed redirect endpoint to forward to your attacker-controlled host.
    
- **Directory traversal** — send payloads like `x/../private` where `../` removes appended path segments.
    

---

# Lab example: Avatar field → access `/private` (TryHackMe style)

**Goal:** get the server to fetch `/private` and display its contents via the avatar feature. The page shows avatar as a `data:` URI (base64 image) after the server fetches the requested resource.

## Flow summary

1. Create an account and sign in.
    
2. Visit `/customers/new-account-page` which offers avatar selection.
    
3. Inspect a radio button for an avatar: the `value` attribute points to a path (e.g. `/images/avatar1.png`).
    
4. Edit the radio button `value` in the browser to a target path that will cause the server to request `/private`.
    
5. Submit the form. The server attempts to fetch the resource and returns the avatar as a `data:` URI (base64). Decode it to reveal the flag.
    

## Concrete steps (commands / actions)

### 1. Inspect the avatar radio input

- Right-click → Inspect on one of the avatar radio buttons.
    
- Look for something like:
    

```html
<input type="radio" name="avatar" value="/images/avatar1.png">
```

### 2. Modify the value to attempt `/private`

- Try directly first:
    

```html
<input type="radio" name="avatar" value="private">
```

- If you get a deny-list error (path cannot start with `/private`) use directory traversal:
    

```html
<input type="radio" name="avatar" value="x/../private">
```

Explanation: when the server constructs/fetches the final path, `x/../private` normalizes to `/private`.

### 3. Select the edited avatar and click **Update Avatar**

- The page will now show your selected avatar and the server-provided image (often embedded as a `data:image/png;base64,....` string).
    

### 4. View page source or inspect the avatar element to find the `data:` URI

You will see something like:

```html
<div class="avatar" style="background-image: url('data:image/png;base64,iVBORw0K...')"></div>
```

### 5. Extract and decode the base64 payload

- Copy just the base64 part after the comma (`iVBORw0K...`).
    
- On Linux/macOS, decode to a file and view:
    

```bash
# save base64 into file
echo 'iVBORw0K...' > avatar.b64

# decode to png
base64 -d avatar.b64 > avatar.png

# open or inspect
file avatar.png
# (or open in an image viewer)
```

- If the returned content is actually text encoded in base64, decode it:
    

```bash
echo 'BASE64STRING' | base64 -d
```

One of these will reveal plain text (in the lab, that text is the flag).

---

# Example payloads (try in this order)

- Direct path: `private`
    
- Directory traversal: `x/../private`
    
- Nested traversal if necessary: `a/b/../../private`
    
- If you can control a URL parameter instead of a path: `http://attacker.com/resource` (for general SSRF tests)
    
- If an allow-list requires `https://website.thm`, try an attacker subdomain that still matches: `https://website.thm.attacker.tld` (only if the application checks prefix incorrectly)
    

---

# Blind SSRF detection

If the server doesn’t return fetched data, use an out-of-band listener to detect the request:

- RequestBin / webhook.site / Burp Collaborator / your own HTTP server
    
- Example: set the URL parameter to `https://your-bin.example/unique-path` and monitor logs for incoming requests from the target server.
    

---

# Mitigations (recommended fixes)

- **Allow-list only**: Accept requests only to a strict allow-list of hostnames/IPs. Prefer explicit host/IP checks rather than naive prefix checks.
    
- **Resolve and verify IPs**: Resolve DNS on the server side and check the _resolved IP_ against allow/deny lists (blocks DNS tricks).
    
- **Block known local ranges**: Deny requests to `127.0.0.0/8`, `10.0.0.0/8`, `169.254.169.254`, `172.16.0.0/12`, `192.168.0.0/16`, and cloud metadata ranges — but also combine with hostname checks and DNS verification.
    
- **Request validation & whitelisting of schemes**: Only allow `https` if appropriate; restrict protocols (no `file:`, `gopher:` etc).
    
- **Avoid fetching arbitrary URLs** unless strictly necessary. If fetching is required, fetch through a proxy that enforces restrictions and logs requests.
    
- **Normalize paths**: If your app appends a base path and allows path input, normalize and sanitize `..` sequences and reject suspicious traversal sequences.
    

---

# Short checklist for a pentest/CTF

- Look for URL/hidden-field/path parameters that control fetching.
    
- Try simple payloads first (`private`, `http://attacker`) then directory traversal (`x/../private`).
    
- Use out-of-band listeners for blind SSRF.
    
- Try numeric/alternate IP encodings, `nip.io` / `.127.0.0.1.nip.io` style DNS tricks for deny-list bypass.
    
- Always record steps and output (screenshots of `data:` URI, the base64 blob, decoded file).
    

---

## Quick reference commands

```bash
# decode base64 to file
echo 'BASE64STRING' | base64 -d > output.bin

# decode file containing base64
base64 -d avatar.b64 > avatar.png
```

---
