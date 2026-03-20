# Sublist3r

## Overview

**Sublist3r** is a fast and powerful subdomain enumeration tool written in Python. It is designed to enumerate subdomains of websites using multiple search engines and online services. The tool is primarily used by penetration testers and security researchers to gather information about the attack surface of a target domain during the reconnaissance phase.

Sublist3r automates the process of discovering subdomains by querying several search engines such as Google, Yahoo, Bing, Baidu, and Ask, as well as public sources like VirusTotal and Netcraft.

---

## Key Features

- Enumerates subdomains using multiple search engines.
    
- Integrates with external services (e.g., VirusTotal, ThreatCrowd).
    
- Supports brute force mode for additional discovery.
    
- Can save results into a text file for further analysis.
    
- Lightweight and easy to use with Python.
    

---

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/aboul3la/Sublist3r.git
cd Sublist3r
```

### 2. Install Dependencies

```bash
sudo apt install python3 python3-pip
pip3 install -r requirements.txt
```

---

## Basic Usage

### Command Syntax

```bash
python3 sublist3r.py -d <domain> [options]
```

### Example

```bash
python3 sublist3r.py -d example.com
```

This command will enumerate all subdomains of `example.com` using multiple search engines and display the results on the terminal.

---

## Common Options

|Option|Description|Example|
|---|---|---|
|`-d`|Specify the target domain|`-d example.com`|
|`-b`|Enable brute forcing using a wordlist|`-b`|
|`-p`|Specify ports to scan on discovered subdomains|`-p 80,443`|
|`-o`|Save results to a text file|`-o subdomains.txt`|
|`-t`|Set number of threads for faster scanning|`-t 10`|
|`-v`|Enable verbose output|`-v`|

---

## Advanced Usage Examples

### Save Results to a File

```bash
python3 sublist3r.py -d example.com -o subdomains.txt
```

Saves all discovered subdomains to `subdomains.txt`.

### Brute Force Mode

```bash
python3 sublist3r.py -d example.com -b
```

Uses a predefined wordlist to brute force additional subdomains that may not appear in public sources.

### Scan Specific Ports on Found Subdomains

```bash
python3 sublist3r.py -d example.com -p 80,443,8080
```

Enumerates subdomains and checks if the specified ports are open.

### Verbose Mode for Detailed Output

```bash
python3 sublist3r.py -d example.com -v
```

Displays detailed information while performing the enumeration.

---

## Example Output

```
[*] Enumerating subdomains for example.com
[-] Searching now in Google..
[-] Searching now in Bing..
[-] Searching now in Yahoo..
[+] Found 12 subdomains:
    - mail.example.com
    - blog.example.com
    - dev.example.com
    - api.example.com
```

---

## Use Case in Cybersecurity

Sublist3r is commonly used during the **reconnaissance phase** of penetration testing to:

- Identify potential attack vectors.
    
- Map the organization’s exposed infrastructure.
    
- Discover forgotten or misconfigured subdomains.
    
- Assist in vulnerability assessment and bug bounty hunting.
    

---

## References

- Official GitHub: [https://github.com/aboul3la/Sublist3r](https://github.com/aboul3la/Sublist3r)
    
- Author: Ahmed Aboul-Ela
    

---

