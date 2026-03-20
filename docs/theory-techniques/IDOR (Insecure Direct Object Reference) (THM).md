## What is an IDOR?

IDOR stands for Insecure Direct Object Reference. It is an access control vulnerability that occurs when an application uses user-supplied input to access objects (for example: database records, files, or resources) and fails to verify that the requesting user is authorized to access the referenced object. If the application trusts the supplied identifier without server-side authorization checks, an attacker who manipulates that identifier can access objects belonging to other users.

---

## Why IDOR matters

- IDORs allow unauthorized data exposure and actions (read, modify, delete) on resources owned by other users.
    
- They are frequently high-impact in multi-tenant or user-account contexts (profiles, invoices, messages, files).
    
- They are often easy to exploit when identifiers are predictable or reversible.
    

---

## Common ID representations

### Encoded IDs (example: base64)

- Developers sometimes encode identifiers for transport or readability (base64 is common).
    
- Encoding is reversible. If you spot an ID that looks like base64 (`A-Za-z0-9+/` plus `=` padding), decode it to reveal the underlying value.
    

**Example**

Cookie or parameter contains:

```
user_id=MTMwNQ==
```

Decode with command line:

```bash
echo 'MTMwNQ==' | base64 --decode
# -> 1305
```

Change value and re-encode:

```bash
echo -n '1000' | base64
# -> MTAwMA==
```

Submit the modified value and observe if the server returns data for user 1000.

### Hashed IDs

- Identifiers may be hashed (MD5, SHA1, etc.). Hashing is one-way but predictable patterns may exist (e.g., hashing an integer). Use online databases (CrackStation) or local cracking tools to attempt reversing weak hashes.
    

**Example**

If you find `id=202cb962ac59075b964b07152d234b70`, this is MD5 of `123`.

Check with CrackStation or `hashcat` wordlists if allowed.

### Unpredictable IDs

- Truly random or sufficiently long unpredictable IDs (UUIDv4, long random tokens) are harder to guess. If IDs are unpredictable, IDOR is less likely via enumeration but still possible via other logic flaws.
    

---

## Where IDORs are commonly found

- Query string parameters (example: `/profile?user_id=1305`)
    
- POST body parameters (JSON or form data)
    
- Cookies or session objects (encoded or JSON stored client-side)
    
- AJAX/XHR requests (check Network tab)
    
- API endpoints (REST, GraphQL)
    
- Hidden form fields or JavaScript variables
    

---

## Detection techniques

1. Identify endpoints that accept identifiers. Look at links, forms, and network requests (browser devtools → Network).
    
2. Try altering identifiers to other values and observe responses.
    
3. Decode encoded IDs (base64/base32) and try modifying underlying values.
    
4. If IDs are numeric or sequential, increment/decrement and test for access.
    
5. If IDs appear hashed, attempt lookup using hash reversal services or cracking tools (only when authorized).
    
6. Create two test accounts and swap IDs to see if one account can access the other’s resources.
    
7. Check responses for differences (HTTP status, JSON fields, error messages, content length).
    
8. Inspect JavaScript for hidden endpoints or parameters (parameter mining).
    

---

## Example: finding IDOR with a JSON API

Scenario: The account page loads user data from `/api/v1/customer?id={user_id}`.

Steps:

1. Open browser devtools → Network and refresh the page.
    
2. Locate the `GET /api/v1/customer?id=1305` request that returns JSON with `id`, `username`, `email`.
    
3. Modify the `id` parameter to another value (for example, `id=1000`) and resend the request.
    
4. If the response returns data for `id=1000` while you are authenticated as a different user, this indicates an IDOR.
    

**Browser PoC (manual):**

- In devtools, right-click the request → "Edit and Resend" (or copy as curl) → change `id` → send.
    

**curl PoC:**

```bash
curl 'http://online-service.thm/api/v1/customer?id=1000' \
  -H 'Cookie: session=<your_session_cookie>'
```

If this returns another user's data while you remain authenticated as your account, document the behavior as IDOR.

---

## Exploitation notes and responsible disclosure

- Exploit only within authorized scope. Never access or exfiltrate sensitive user data outside scope.
    
- Prefer read-only probing when discovering vulnerability; do not modify or delete other users' data.
    
- When testing, create controlled test accounts to minimize impact.
    
- Record reproducible steps: request, modified parameter, responses, timestamps, and any identifying payloads.
    

---

## Mitigations and best practices

- Implement server-side authorization checks for every request that accesses a user-specific object. Example: after retrieving the object by ID, verify `object.owner_id == current_user.id`.
    
- Avoid relying on obscurity (encoding) as an access control mechanism.
    
- Use unpredictable, non-sequential identifiers where appropriate, but do not rely on them alone — always enforce authorization checks.
    
- For APIs, ensure access tokens and scopes are properly validated.
    
- Sanitize and validate all user-controlled inputs server-side.
    
- Log access attempts that reference resources not owned by the caller and alert on anomalies.
    

---

## Quick pentester checklist

1. Confirm authorization and scope.
    
2. Identify endpoints and parameters that reference objects.
    
3. Test simple numeric increments/decrements.
    
4. Decode/encode suspicious-looking identifiers (base64).
    
5. Check for hashed IDs and attempt safe reversal only when permitted.
    
6. Create multiple accounts to test cross-account access.
    
7. Use browser devtools to edit/resend requests and observe server behavior.
    
8. Document PoC steps and avoid harmful actions.
    

---

## Reporting template (PoC)

**Title:** IDOR allows access to other users' profile data via `id` parameter

**Summary:** The endpoint `/api/v1/customer?id={user_id}` returns user data and does not verify the requesting user's ownership of the resource. By changing the `id` parameter, an authenticated user can view other users' profiles.

**Impact:** Exposure of personal data and potential privacy breach. Depending on the fields returned, this may permit social engineering, account targeting, or further attacks.

**Steps to reproduce:**

1. Login as test user `alice`.
    
2. Observe request: `GET /api/v1/customer?id=1305` returns `alice` data.
    
3. Modify request to `GET /api/v1/customer?id=1000` while retaining `alice` session cookie.
    
4. Observe that response contains another user's data (example: `"id":1000, "username": "bob", "email": "bob@example.com"`).
    

**Proof:** Include request/response pairs (sanitized) and timestamps.

**Remediation:**

- Verify ownership server-side for all direct object references.
    
- If using encoded IDs, still enforce authorization checks.
    
- Consider returning an authorization error (403) when the requester is not permitted to access the referenced object.
    

---

## Legal and ethical note

Only perform IDOR testing on systems where you have explicit permission. Unauthorized testing is illegal and unethical.

---
