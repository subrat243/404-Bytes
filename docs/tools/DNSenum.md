# DNSenum

## What is DNSenum?

DNSenum is a multithreaded Perl script designed for **DNS enumeration**. Its primary purpose is to gather as much information as possible about a target domain. It can discover various DNS records (such as A, NS, and MX), attempt zone transfers, perform subdomain brute-forcing, and conduct reverse lookups on netranges. It is a fundamental tool used during the information-gathering phase of a security assessment or penetration test.

---

## Key Features

- **Host Information**: Retrieves host addresses (A records).
    
- **Name Servers**: Gathers authoritative name servers (NS records).
    
- **Mail Servers**: Collects mail exchange server information (MX records).
    
- **Zone Transfer**: Attempts an AXFR query on all discovered name servers to get a complete list of hostnames, which, if successful, can reveal the entire DNS zone file.
    
- **Subdomain Brute-Forcing**: Uses a built-in or user-provided wordlist to discover subdomains and hostnames.
    
- **Google Scraping**: Scrapes Google search results to find additional subdomains indexed by the search engine.
    
- **Reverse Lookups**: Performs reverse lookups on C-class network ranges to identify adjacent hosts and map out network blocks.
    
- **Output**: Saves the results in a clear, human-readable text format or a structured XML file for easy parsing.
    

---

## Installation

DNSenum comes pre-installed on most penetration testing distributions like **Kali Linux** and **Parrot OS**. If it is not installed, you can typically install it using the system's package manager.

**For Debian/Ubuntu-based systems:**

Bash

```bash
sudo apt-get update
sudo apt-get install dnsenum
```

If you are installing from source, you may also need to install its Perl dependencies:

Bash

```bash
sudo cpan install Net::DNS Net::IP Net::Netmask XML::Writer
```

---

## Usage

The basic syntax for running DNSenum is as follows:

Bash

```bash
dnsenum [options] <domain>
```

### Common Options

- `--noreverse`: Skips the reverse lookup operations. This can significantly speed up the scan.
    
- `-f, --file <file>`: Specifies a wordlist file to use for subdomain brute-forcing instead of the default `dns.txt`.
    
- `-o, --output <file.xml>`: Specifies an output file to save the results in XML format.
    
- `--threads <value>`: Sets the number of threads to use for parallel processing of tasks like brute-forcing and reverse lookups.
    
- `-v, --verbose`: Enables verbose output, showing more details about the process in real-time.
    
- `--private`: Shows and saves the private IP addresses from the results.
    
- `-r, --recurse`: Recurses on any subdomains that are found, performing the same enumeration checks on them.
    

---

## Example Scan

Here is an example command to perform a comprehensive, yet time-efficient, scan on a target domain. This command will be verbose, use 10 threads, skip reverse lookups, and use a custom wordlist.

**Command:**

Bash

```bash
dnsenum --threads 10 -v --noreverse -f /usr/share/wordlists/dns/subdomains-top1mil-5000.txt example.com -o results.xml
```

### Breakdown of the Command

- `dnsenum`: The executable script.
    
- `--threads 10`: Uses 10 concurrent threads for faster processing.
    
- `-v`: Enables verbose mode for detailed progress updates.
    
- `--noreverse`: Disables the time-consuming reverse lookup phase.
    
- `-f /usr/share/wordlists/dns/subdomains-top1mil-5000.txt`: Specifies the path to a custom wordlist file for subdomain brute-forcing.
    
- `example.com`: The target domain for enumeration.
    
- `-o results.xml`: Saves the output to a file named `results.xml` in XML format.
    

### Expected Output

The tool will begin by performing standard DNS queries for host addresses, name servers, and mail servers. It will then attempt a zone transfer against each name server. Following that, it will initiate the subdomain brute-force attack using the specified wordlist. All discovered hosts, IP addresses, and other DNS records will be displayed in the terminal and saved to the `results.xml` file.