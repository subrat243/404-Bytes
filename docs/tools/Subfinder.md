## Overview

**Subfinder** is a fast and powerful subdomain discovery tool designed to find valid subdomains for websites. It is developed by **ProjectDiscovery** and is widely used in bug bounty, penetration testing, and reconnaissance. Subfinder uses passive online sources, DNS enumeration, and certificate transparency logs to gather subdomains efficiently without making direct DNS brute-force attempts.

Subfinder helps security professionals identify potential attack surfaces by mapping all subdomains associated with a target domain, allowing for further scanning, enumeration, or vulnerability testing.

---

## Key Features

- **Passive Enumeration:** Uses a large collection of passive data sources to avoid noisy network requests.
    
- **Multi-source Integration:** Aggregates results from public APIs like VirusTotal, Censys, Shodan, and others.
    
- **JSON and Text Output:** Supports multiple output formats for automation and scripting.
    
- **Concurrency Support:** Performs queries in parallel, resulting in high speed.
    
- **Configurable API Keys:** Allows integration of multiple API services using a configuration file.
    
- **Integration Friendly:** Works seamlessly with other tools like `httprobe`, `httpx`, `nuclei`, and `amass`.
    

---

## Installation

### Using Go

```bash
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
```

### Using Binary (Linux Example)

```bash
wget https://github.com/projectdiscovery/subfinder/releases/latest/download/subfinder-linux-amd64.tar.gz
tar -xvf subfinder-linux-amd64.tar.gz
sudo mv subfinder /usr/local/bin/
```

### Verify Installation

```bash
subfinder -version
```

---

## Configuration

Subfinder requires API keys for certain passive sources. To configure them:

1. Run Subfinder once to generate a default config file:
    
    ```bash
    subfinder
    ```
    
2. The config file will be created at:
    
    ```
    ~/.config/subfinder/config.yaml
    ```
    
3. Edit the file and add your API keys for various services like VirusTotal, Shodan, Censys, etc.
    

Example snippet from `config.yaml`:

```yaml
sources:
  - alienvault
  - censys
  - certspotter
  - shodan
  - virustotal

keys:
  censys: CENSYS_API_ID:CENSYS_API_SECRET
  virustotal: VIRUSTOTAL_API_KEY
  shodan: SHODAN_API_KEY
```

---

## Basic Usage

### Discover subdomains for a single target

```bash
subfinder -d example.com
```

### Save results to a file

```bash
subfinder -d example.com -o subdomains.txt
```

### JSON output

```bash
subfinder -d example.com -oJ output.json
```

### Input list of domains

```bash
subfinder -dL domains.txt -o results.txt
```

### Use specific sources only

```bash
subfinder -d example.com -sources censys,shodan,virustotal
```

### Combine with `httpx` for live host probing

```bash
subfinder -d example.com -silent | httpx -title -status-code
```

---

## Example Recon Pipeline

```bash
subfinder -d example.com -silent | httpx -silent | nuclei -t cves/
```

1. Subfinder enumerates subdomains.
    
2. httpx filters for live hosts and gathers HTTP metadata.
    
3. Nuclei scans live hosts for vulnerabilities.
    

---

## Troubleshooting & Tips

### 1. Missing expected results

- Ensure API keys are correctly configured in `~/.config/subfinder/config.yaml`. Some sources return richer results only when authenticated.
    
- Run with `-sources` to explicitly include sources you expect:
    
    ```bash
    subfinder -d example.com -sources certspotter,virustotal
    ```
    
- Try running multiple passive runs at different times; some data sources update at different intervals.
    

### 2. Rate limits and API throttling

- Public APIs and paid services enforce rate limits. If you see partial results or failures, confirm API quotas on the provider dashboard.
    
- Space out requests or add retries in automation. When integrating into CI/pipelines, stagger runs or rotate API keys if permitted.
    

### 3. Slow or hanging runs

- Use `-silent` to reduce output noise in scripts:
    
    ```bash
    subfinder -d example.com -silent
    ```
    
- If a single source hangs, run with a smaller set of sources using `-sources`.
    
- Ensure network connectivity to the external APIs; intermittent connectivity can cause delays.
    

### 4. False positives / incorrect hostnames

- Subfinder aggregates from many sources; some entries may be outdated or wildcard-generated. Validate findings with DNS resolution or `httpx`:
    
    ```bash
    subfinder -d example.com -silent | httpx -silent
    ```
    
- Use `massdns` or `dig` for bulk DNS validation if needed.
    

### 5. Proxy, corporate network, and VPN usage

- If you must route traffic through a proxy, configure your environment or toolchain accordingly. Subfinder itself respects standard environment proxy variables (`HTTP_PROXY`, `HTTPS_PROXY`). Example:
    
    ```bash
    export HTTP_PROXY="http://127.0.0.1:8080"
    export HTTPS_PROXY="http://127.0.0.1:8080"
    subfinder -d example.com
    ```
    
- When behind corporate networks with restricted access, run Subfinder from a network that allows outbound connections to the public APIs used.
    

### 6. Handling large outputs and deduplication

- Subfinder deduplicates results, but when chaining tools, use `sort -u` or `uniq` to ensure unique entries:
    
    ```bash
    subfinder -dL domains.txt -silent | sort -u > unique_subs.txt
    ```
    

### 7. Integration gotchas

- When piping to tools like `httpx` or `nuclei`, prefer `-silent` to reduce noisy logs and speed up processing:
    
    ```bash
    subfinder -d example.com -silent | httpx -silent | nuclei -silent
    ```
    
- Confirm expected input format for downstream tools (plain hostname per line vs. URL).
    

### 8. Updates and compatibility

- Keep Subfinder updated; ProjectDiscovery periodically adds sources and fixes. Reinstall via `go install ...@latest` or download the latest binary.
    
- When updating, re-check config and API compatibility; occasionally field names or auth formats change for third-party APIs.
    

### 9. Respect legal and ethical boundaries

- Only run Subfinder and follow-up scans against domains and systems you are authorized to test. Passive enumeration can still implicate you if performed at scale against targets you do not own or have permission to test.
    

### 10. Debugging and verbose output

- If behavior is unexpected, run without `-silent` and inspect errors returned by Subfinder. Logs often indicate which source produced an error or was rate-limited. Capture stderr for debugging:
    
    ```bash
    subfinder -d example.com 2>subfinder_errors.log
    ```
    

---

## Common Use Cases

- Bug bounty reconnaissance.
    
- Attack surface mapping for security assessments.
    
- Continuous monitoring to detect new subdomains over time.
    
- Feeding validated subdomains into vulnerability scanners or asset inventory systems.
    

---

## Example Output

```
blog.example.com
mail.example.com
shop.example.com
dev.example.com
```

---

## References

- Official repository: [https://github.com/projectdiscovery/subfinder](https://github.com/projectdiscovery/subfinder)
    
- Official docs: [https://docs.projectdiscovery.io/tools/subfinder](https://docs.projectdiscovery.io/tools/subfinder)
    

---
