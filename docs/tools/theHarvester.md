## Overview

**theHarvester** is an open-source reconnaissance tool used to collect information about a target domain or organization from publicly available sources (OSINT). It helps ethical hackers, penetration testers, and security analysts gather critical details like email addresses, subdomains, IPs, and employee names during the **information gathering** phase of penetration testing.

theHarvester is especially useful for **passive reconnaissance**, meaning it doesn’t directly interact with the target, reducing the chances of detection.

---

## Key Features

- Collects **email addresses** from multiple public sources (Google, Bing, LinkedIn, etc.)
    
- Enumerates **subdomains** using search engines and DNS data
    
- Gathers **hosts and IPs** through passive DNS lookup
    
- Supports **Shodan**, **Censys**, and **Netcraft** for extended reconnaissance
    
- Generates **HTML, XML, and JSON** reports
    
- Supports **DNS brute-forcing** and **virtual host discovery**
    

---

## Installation

### On Kali Linux

theHarvester is pre-installed on Kali Linux. To verify:

```bash
theHarvester -h
```

If not installed, you can manually install it using:

```bash
sudo apt update
sudo apt install theharvester
```

### From Source (Manual Installation)

```bash
git clone https://github.com/laramies/theHarvester.git
cd theHarvester
pip3 install -r requirements.txt
python3 theHarvester.py -h
```

---

## Basic Syntax

```bash
theHarvester -d <domain> -b <data source> [options]
```

### Parameters

|Parameter|Description|
|---|---|
|`-d`|Target domain name (e.g., example.com)|
|`-b`|Data source (e.g., google, bing, crtsh, linkedin, shodan, all)|
|`-l`|Limit the number of results to fetch|
|`-f`|Output file for report (supports HTML, XML, JSON)|
|`-v`|Enable verbose output|
|`-h`|Show help message|

---

## Example Usages

### 1. Basic Domain Enumeration

Collect information about a domain using Google as the source:

```bash
theHarvester -d example.com -b google
```

### 2. Using Multiple Sources

Gather data from all supported public sources:

```bash
theHarvester -d example.com -b all
```

### 3. Limiting Search Results

Restrict results to 100 entries:

```bash
theHarvester -d example.com -b bing -l 100
```

### 4. Saving Output to a Report

Save collected data to an HTML file for reporting:

```bash
theHarvester -d example.com -b all -l 200 -f report_example
```

This will create `report_example.html` in the working directory.

### 5. Using Shodan for Host Information

Use Shodan to gather information about exposed services and IPs:

```bash
theHarvester -d example.com -b shodan
```

---

## Common Data Sources

|Source|Description|
|---|---|
|`google`|Google Search Engine|
|`bing`|Microsoft Bing|
|`crtsh`|Certificate Transparency Logs|
|`linkedin`|Public LinkedIn Profiles|
|`shodan`|Shodan IoT Search Engine|
|`duckduckgo`|DuckDuckGo Search Engine|
|`virustotal`|VirusTotal Passive DNS|
|`all`|Use all supported data sources|

---

## Example Output

A sample result may look like:

```
[*] Emails found:
-----------------
admin@example.com
support@example.com

[*] Hosts found:
----------------
mail.example.com: 192.168.1.10
vpn.example.com: 192.168.1.11
web.example.com: 192.168.1.12
```

---

## Best Practices

- Use specific data sources for targeted and efficient results.
    
- Combine with other tools (e.g., `sublist3r`, `amass`, `dnsrecon`) for comprehensive reconnaissance.
    
- Always ensure you have **authorization** before performing scans.
    
- Use `-f` option to generate structured reports for professional documentation.
    

---

## Use Case Scenario

A penetration tester preparing for an assessment can use `theHarvester` to gather employee emails and subdomains before launching social engineering or vulnerability scanning phases.

For example:

```bash
theHarvester -d targetcompany.com -b all -l 500 -f target_report
```

This command gathers up to 500 results from all available sources and stores them in a report named `target_report.html`.

---

## Conclusion

**theHarvester** is a powerful, easy-to-use OSINT tool for reconnaissance and information gathering. It helps identify potential attack surfaces and exposed assets by aggregating publicly available information efficiently.

---
