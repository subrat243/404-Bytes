# DNSRecon

## Overview

**DNSRecon** is a powerful DNS enumeration and reconnaissance tool written in Python. It is used by cybersecurity professionals and penetration testers to gather DNS-related information about a target domain. The tool performs multiple types of DNS queries to identify misconfigurations, exposed hosts, and potential attack vectors within a domain’s DNS infrastructure.

DNSRecon supports **brute-force enumeration**, **zone transfers**, **reverse lookups**, **cache snooping**, and **record enumeration**, making it a versatile tool for both offensive and defensive security assessments.

---

## Key Features

- **Standard Enumeration**: Performs standard record enumeration for A, AAAA, MX, NS, SOA, and TXT records.
    
- **Zone Transfer Testing (AXFR)**: Attempts zone transfers to identify exposed DNS records from misconfigured DNS servers.
    
- **Reverse Lookups**: Identifies PTR records for a given IP range to discover associated hostnames.
    
- **Brute Force Enumeration**: Performs dictionary-based subdomain enumeration using a provided wordlist.
    
- **Google & Bing Queries**: Uses search engines to identify subdomains.
    
- **SRV Record Enumeration**: Looks for service records (e.g., SIP, LDAP, Kerberos).
    
- **Cache Snooping**: Tests if DNS servers have cached specific records, revealing information about network usage.
    
- **DNSSEC Testing**: Checks for DNSSEC configuration and validates signatures.
    

---

## Installation

### From Package Manager

On Kali Linux or Parrot OS, DNSRecon is pre-installed.  
If not, you can install it using:

```bash
sudo apt install dnsrecon
```

### From Source (Manual Installation)

```bash
git clone https://github.com/darkoperator/dnsrecon.git
cd dnsrecon
sudo python3 setup.py install
```

---

## Basic Syntax

```bash
dnsrecon [options] -d <domain>
```

---

## Usage Examples

### 1. Standard DNS Enumeration

Performs a standard record lookup for all major DNS record types.

```bash
dnsrecon -d example.com
```

**Explanation:**  
This retrieves A, AAAA, MX, NS, SOA, and TXT records for the target domain.

---

### 2. Zone Transfer Attempt

Attempts a zone transfer (AXFR) to identify misconfigurations.

```bash
dnsrecon -d example.com -t axfr
```

**Explanation:**  
If the DNS server is misconfigured, it will leak the entire zone file, revealing all internal hostnames.

---

### 3. Brute-Force Subdomain Enumeration

Discovers subdomains using a wordlist.

```bash
dnsrecon -d example.com -D /usr/share/wordlists/dnsmap.txt -t brt
```

**Explanation:**  
This performs a dictionary attack using the provided wordlist to find hidden or unlisted subdomains.

---

### 4. Reverse Lookup for an IP Range

Finds domains associated with an IP range.

```bash
dnsrecon -r 192.168.1.0/24 -n 8.8.8.8
```

**Explanation:**  
Performs reverse lookups (PTR queries) on each IP address in the given subnet using Google’s DNS server.

---

### 5. DNSSEC Enumeration

Checks for DNSSEC configuration on the target domain.

```bash
dnsrecon -d example.com -t dnssec
```

**Explanation:**  
Identifies whether DNSSEC is enabled and retrieves related information.

---

### 6. Output Results to a File

Saves scan results in XML format.

```bash
dnsrecon -d example.com -t std --xml output.xml
```

**Explanation:**  
This allows storing the results for later analysis or reporting.

---

## Common Options

|Option|Description|
|---|---|
|`-d DOMAIN`|Target domain name|
|`-n NS_SERVER`|Use a specific nameserver|
|`-r RANGE`|Perform reverse lookup on an IP range|
|`-D WORDLIST`|Path to wordlist for brute-force|
|`-t TYPE`|Type of enumeration (std, brt, axfr, rev, srv, snoop, dnssec)|
|`--xml FILE`|Output results to an XML file|
|`--json FILE`|Output results to a JSON file|

---

## Practical Use in Penetration Testing

DNSRecon is typically used in the **reconnaissance phase** of a penetration test to identify potential entry points such as forgotten subdomains, exposed DNS records, or misconfigured name servers. It provides valuable intelligence for **attack surface mapping** and **network footprinting**.

---

## Example Workflow

1. Perform a standard DNS enumeration to identify primary records.
    
2. Attempt a zone transfer on discovered name servers.
    
3. Brute-force subdomains using a common wordlist.
    
4. Conduct reverse lookups on related IP ranges.
    
5. Save results for documentation and further analysis.
    

---

## References

- GitHub Repository: [https://github.com/darkoperator/dnsrecon](https://github.com/darkoperator/dnsrecon)
    
- Official Documentation: [https://tools.kali.org/information-gathering/dnsrecon](https://tools.kali.org/information-gathering/dnsrecon)
    

---
