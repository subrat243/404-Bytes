Dirb is a command-line tool designed to discover hidden files and directories on web servers. It functions by launching a dictionary-based brute force attack, helping security researchers and penetration testers identify potentially vulnerable or unlisted content.

---

## How It Works

Dirb works by taking a wordlist (a file containing a list of common names for files and directories) and making HTTP requests for each word on that list against a target URL. It then analyzes the server's HTTP response codes to determine if a file or directory exists at that path.

- A **200 OK** response typically indicates a found resource.
    
- A **301 Moved Permanently** or **302 Found** response indicates a redirection, often for a directory.
    
- A **403 Forbidden** response means the resource exists but is not accessible.
    
- A **404 Not Found** response means the resource does not exist.
    

---

## Installation

Dirb comes pre-installed on many penetration testing distributions like Kali Linux. If it is not installed, you can typically install it using your distribution's package manager.

For Debian-based systems (like Ubuntu):

Bash

```
sudo apt-get update
sudo apt-get install dirb
```

---

## Usage and Examples

The basic syntax for using Dirb is:

Bash

```
dirb <url_base> [wordlist_file(s)] [options]
```

### Basic Scan

This command launches a scan against the target URL using Dirb's default wordlist.

Bash

```
dirb http://example.com
```

### Using a Custom Wordlist

To use a specific wordlist for the scan, provide the path to the file. Kali Linux includes many wordlists in the `/usr/share/wordlists/dirb/` directory.

Bash

```
dirb http://example.com /usr/share/wordlists/dirb/common.txt
```

### Saving Output to a File

You can save the scan results to a text file for later analysis using the `-o` flag.

Bash

```
dirb http://example.com -o scan_results.txt
```

### Recursive Scanning

To perform a recursive scan, where Dirb also scans the directories it discovers, use the `-r` flag.

Bash

```
dirb http://example.com -r
```

### Scanning for Specific File Extensions

If you are looking for files with a particular extension (e.g., `.php`, `.config`), use the `-X` flag followed by the extensions.

Bash

```
dirb http://example.com -X .php,.html,.js
```

### Ignoring Specific HTTP Response Codes

To fine-tune the results and ignore certain HTTP status codes (like "404 Not Found"), use the `-N` flag.

Bash

```
dirb http://example.com -N 404
```

---

## Disclaimer

This tool should only be used for educational purposes and on systems for which you have explicit, written permission. Unauthorized scanning of web servers is illegal.