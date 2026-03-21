# Nmap

Comprehensive guide to Nmap for network discovery, port scanning, service version detection, and OS fingerprinting. Covers advanced techniques like NSE scripts, evasion methods, and integration with other security tools.

**Purpose**: To master network reconnaissance and efficiently identify attack surfaces in target environments.

---

## Key Concepts

- Host Discovery
- Port Scanning
- Service Version Detection
- OS Fingerprinting
- NSE Scripts
- Evasion Techniques
- Network Reconnaissance

---

## Examples

```bash
# Basic scan
nmap -sV -O [IP_ADDRESS]

# Aggressive scan
nmap -A [IP_ADDRESS]

# Scan specific ports
nmap -p 22,80,443 [IP_ADDRESS]

# NSE script execution
nmap --script vuln [IP_ADDRESS]

# Save output
nmap -oN output.txt [IP_ADDRESS]
```
---
