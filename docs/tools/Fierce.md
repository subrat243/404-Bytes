# Fierce

## Introduction

Fierce is a DNS reconnaissance tool designed for network mapping and footprinting. Its primary purpose is to discover hostnames and non-contiguous IP blocks associated with a target domain. It is a classic tool used by penetration testers and security analysts during the initial information-gathering phase of a security assessment.

Fierce automates the process of subdomain enumeration and reverse DNS lookups to build a comprehensive picture of a target's network presence, often uncovering misconfigured servers or forgotten assets that are not publicly linked.

## Core Functionality

Fierce operates through a sequence of techniques to gather information. The typical workflow is as follows:

1.  **Initial DNS Query:** It begins by querying the target domain's authoritative Name Servers (NS) and Mail Exchanger (MX) records to identify primary servers.

2.  **Zone Transfer Attempt:** Fierce attempts to perform a DNS Zone Transfer (AXFR) against each identified name server. A successful zone transfer provides a complete list of all DNS records for the domain. This is rarely successful on properly configured servers but is a high-impact finding if it works.

3.  **Subdomain Brute-force:** If the zone transfer fails, Fierce uses an internal or user-specified wordlist to brute-force common subdomain names (e.g., `www`, `mail`, `dev`, `api`, `vpn`). It sends a DNS query for each potential subdomain to see if it resolves to an IP address.

4.  **Reverse DNS Lookup (PTR Scan):** This is one of Fierce's most powerful features. For each IP address discovered, Fierce identifies the Class C network range (the surrounding `/24` block) and performs a reverse DNS lookup (PTR query) on every IP address within that range. This process can uncover hostnames that were not found during the brute-force attack, revealing internal naming conventions and additional servers within the target's IP space.

5.  **Wildcard Detection:** Fierce also checks for wildcard DNS records (`*.example.com`). This prevents the tool from reporting a large number of false positives during the brute-force phase.

## Installation

Fierce is a Perl script that comes pre-installed on many security-focused operating systems, such as Kali Linux. If it is not installed, you can typically find it in your distribution's package repository or download it from its source.

A popular modern Python port is also available and can be installed via `git`.

**For Kali Linux / Debian-based systems:**
```bash
sudo apt-get update
sudo apt-get install fierce
````

## Usage

The basic syntax for running Fierce is straightforward. The most critical argument is specifying the target domain.

### General Syntax

```bash
fierce --domain <target_domain> [options]
```

### Common Options

- `--domain <domain>`: The target domain to scan. (Required)
    
- `--wordlist <file>`: Specify a custom wordlist file for the subdomain brute-force.
    
- `--dns-server <server>`: Use a specific DNS server for all queries. Useful for bypassing local DNS filters or testing a specific resolver.
    
- `--threads <number>`: Specify the number of threads to use for scanning to speed up the process.
    
- `--verbose`: Provides detailed, real-time output of the tool's actions.
    
- `--connect`: Attempts to perform an HTTP connection on discovered hosts to check for web servers.
    

## Usage Examples

Below are some practical examples of how to use Fierce. For these examples, we will use `example.com` as the target domain.

### Example 1: Basic Scan

This is the most common use case. It performs all standard checks (zone transfer, brute-force with default list, reverse lookups) against the target domain.

**Command:**

Bash

```bash
fierce --domain example.com
```

Explanation:

This command will start the reconnaissance process on example.com. It will first try a zone transfer. Upon failure, it will use its built-in wordlist to find subdomains. Finally, it will perform reverse DNS lookups on the network blocks of the discovered IPs.

### Example 2: Scan with a Custom Wordlist and DNS Server

This example is useful when you have a specialized wordlist tailored to the target or want to use a public DNS resolver like Google's.

**Command:**

Bash

```bash
fierce --domain example.com --wordlist /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt --dns-server 8.8.8.8
```

**Explanation:**

- `--wordlist ...`: Tells Fierce to use a specific, more extensive wordlist for the brute-force attack instead of its default one.
    
- `--dns-server 8.8.8.8`: All DNS queries will be sent to Google's public DNS server.
    

### Example 3: Aggressive and Verbose Scan

This command increases the speed of the scan by using multiple threads and provides detailed output to monitor the process in real-time.

**Command:**

Bash

```bash
fierce --domain example.com --threads 25 --verbose
```

**Explanation:**

- `--threads 25`: Fierce will use 25 concurrent threads for its brute-force and reverse lookup tasks, significantly speeding up the scan on large networks.
    
- `--verbose`: The console will display which subdomain is being tested or which IP range is being scanned, providing insight into the tool's progress.
    

## Interpreting the Output

After a successful scan, Fierce will present a summary of its findings, which typically includes:

- A list of discovered name servers.
    
- Results of the zone transfer attempt.
    
- A list of all found hostnames and their corresponding IP addresses.
    
- Results from the reverse DNS lookups, which may show hostnames that do not belong to the target domain but reside on the same network block.
    

This information is critical for building a map of the target's infrastructure and identifying potential entry points for further testing.