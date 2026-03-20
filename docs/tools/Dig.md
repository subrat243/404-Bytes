# Dig

`dig` (Domain Information Groper) is a powerful command-line tool used for querying DNS (Domain Name System) servers. It is commonly used by network administrators, cybersecurity professionals, and developers to perform DNS lookups, troubleshoot DNS-related issues, and verify DNS configurations.

It replaces older tools like `nslookup` and `host` due to its flexibility, advanced query options, and structured output.

---

## Key Features

- Performs **DNS lookups** for A, AAAA, MX, TXT, NS, CNAME, SOA, and other record types
    
- Queries **specific DNS servers**
    
- Displays **detailed query and response sections**
    
- Supports **reverse DNS lookups**
    
- Enables **trace mode** for following the resolution path step-by-step
    
- Allows **batch queries** using input files
    

---

## Basic Syntax

```bash
dig [@server] [domain] [type]
```

**Where:**

- `@server` – (optional) DNS server to query (e.g., `@8.8.8.8` for Google DNS)
    
- `domain` – Domain name to query (e.g., `example.com`)
    
- `type` – Record type to query (e.g., `A`, `MX`, `NS`, etc.)
    

---

## Common Usage Examples

### 1. Basic A Record Lookup

Retrieve the IP address of a domain:

```bash
dig example.com
```

**Explanation:**  
This queries the default DNS server for the A record of `example.com`.

---

### 2. Query a Specific DNS Server

Use a specific DNS server like Google DNS (8.8.8.8):

```bash
dig @8.8.8.8 example.com
```

**Explanation:**  
This sends the DNS query to Google’s public DNS server instead of the system’s default resolver.

---

### 3. Lookup Specific Record Types

**MX Record (Mail Exchange):**

```bash
dig example.com MX
```

**NS Record (Name Server):**

```bash
dig example.com NS
```

**TXT Record:**

```bash
dig example.com TXT
```

**Explanation:**  
Specifying the record type retrieves only that specific type of DNS record.

---

### 4. Reverse DNS Lookup

Find the domain name associated with an IP address:

```bash
dig -x 8.8.8.8
```

**Explanation:**  
Performs a reverse DNS lookup on the given IP address.

---

### 5. Short Answer (Simplified Output)

Get only the essential answer:

```bash
dig +short example.com
```

**Explanation:**  
The `+short` option suppresses all detailed sections and displays only the IP or record value.

---

### 6. Trace the DNS Resolution Path

View each step of DNS resolution:

```bash
dig +trace example.com
```

**Explanation:**  
Displays how the query travels from the root servers to the authoritative server.

---

### 7. Show Only the Answer Section

```bash
dig example.com +noall +answer
```

**Explanation:**  
Suppresses all output except the final answer, useful for scripting or clean results.

---

### 8. Check Multiple Domains from a File

```bash
dig -f domains.txt
```

**Explanation:**  
Performs DNS lookups for all domains listed in `domains.txt` (one domain per line).

---

### 9. Query Specific Port (Non-Standard DNS)

```bash
dig @8.8.8.8 -p 5353 example.com
```

**Explanation:**  
Specifies a non-default port (useful for testing DNS servers on different ports).

---

## Output Breakdown

Typical `dig` output contains several sections:

1. **Header:** Status of query (NOERROR, SERVFAIL, etc.)
    
2. **QUESTION SECTION:** Shows what was asked
    
3. **ANSWER SECTION:** Displays the resolved records
    
4. **AUTHORITY SECTION:** Lists authoritative name servers
    
5. **ADDITIONAL SECTION:** Includes extra information like IPs of name servers
    

---

## Practical Use Cases

- **Penetration Testing:** Identify DNS misconfigurations or information leaks
    
- **Network Diagnostics:** Verify DNS resolution or detect propagation delays
    
- **System Administration:** Check MX or NS records for email or domain configuration
    
- **Incident Response:** Trace domain redirections or phishing domain origins
    

---

## Example Output

```bash
$ dig example.com +noall +answer

example.com.     3600    IN    A    93.184.216.34
```

---

## Summary

|Task|Command Example|Description|
|---|---|---|
|Basic lookup|`dig example.com`|Get A record|
|Specific server|`dig @8.8.8.8 example.com`|Query Google DNS|
|Mail records|`dig example.com MX`|Get mail servers|
|Reverse lookup|`dig -x 8.8.8.8`|Get domain from IP|
|Trace query|`dig +trace example.com`|Follow DNS resolution|
|Short output|`dig +short example.com`|Clean output|

---

## Related Tools

- `nslookup` – Older DNS query tool
    
- `host` – Simplified DNS lookup command
    
- `whois` – Retrieves domain registration details
    

---
