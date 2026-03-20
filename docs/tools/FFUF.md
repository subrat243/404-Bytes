`ffuf` (Fuzz Faster U Fool) is a high-performance web fuzzer written in the Go programming language. It is designed for speed and is a staple tool for security researchers and penetration testers. Its primary purpose is to discover hidden files, directories, subdomains, or GET/POST parameters by bruteforcing web servers with a given wordlist.

## Key Features

- **Speed:** Blazingly fast due to its concurrent nature.
    
- **Flexibility:** Supports fuzzing directories, files, vhosts (subdomains), and request parameters.
    
- **Powerful Filtering:** Easily filter out unwanted results based on status codes, response size, words, or regular expressions.
    
- **Multiple Input Sources:** Can take input from wordlists, stdin, and more.
    
- **Recursive Scanning:** Can automatically scan directories it discovers.
    
- **Versatile Output:** Supports various output formats like JSON, eJSON, HTML, Markdown, CSV, and eCSV.
    
- **Proxy Support:** Can route traffic through proxies like Burp Suite or OWASP ZAP.
    

## Installation

The most common way to install `ffuf` is using Go's package manager.

```bash
go install github.com/ffuf/ffuf/v2@latest
```

Alternatively, on systems with package managers like APT (Debian/Ubuntu) or Homebrew (macOS):

```bash
# FFUF
sudo apt update && sudo apt install ffuf

# For macOS
brew install ffuf
```

## Basic Usage

The core concept of `ffuf` revolves around placing the `FUZZ` keyword in the URL. `ffuf` replaces this keyword with each payload from the specified wordlist.

The basic syntax is:

```bash
ffuf -w <wordlist> -u <URL>
```

- `w`: Specifies the path to the wordlist file.
    
-  `u`: Specifies the target URL, including the `FUZZ` keyword.
	

---

## Usage Examples

### 1. Directory and File Discovery

This is the most common use case. The goal is to find hidden directories and files on a web server.

```bash
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://example.com/FUZZ
```

- **Explanation:** `ffuf` will take each word from `directory-list-2.3-medium.txt` and make a request to `http://example.com/<word>`. By default, it hides 404 responses.
    

### 2. Subdomain Enumeration (Virtual Host Fuzzing)

You can discover subdomains by placing the `FUZZ` keyword in the host part of the URL.

```bash
ffuf -w /path/to/subdomains.txt -u http://FUZZ.example.com -H "Host: FUZZ.example.com"
```

- **Explanation:** `ffuf` will try each word in `subdomains.txt` as a subdomain of `example.com`. The `-H` flag sets the necessary `Host` header.
    

### 3. Fuzzing for Specific File Extensions

If you are looking for files with certain extensions (e.g., `.php`, `.bak`, `.config`), you can use the `-e` flag.

```bash
ffuf -w /path/to/wordlist.txt -u http://example.com/FUZZ -e .php,.html,.bak,.txt
```

- **Explanation:** For each word in the wordlist, `ffuf` will try appending each of the specified extensions. For example, if the word is `admin`, it will test `admin.php`, `admin.html`, `admin.bak`, and `admin.txt`.
    

### 4. Filtering Results

Filtering is crucial for reducing noise and focusing on interesting results.

- Filter by Status Code (-fc):
    
    To filter out common "Not Found" (404) and "Forbidden" (403) responses.
    
    ```bash
    ffuf -w wordlist.txt -u http://example.com/FUZZ -fc 403,404
    ```
    
- Filter by Response Size (-fs):
    
    If a "Not Found" page always returns a specific size (e.g., 1250 bytes), you can filter it out.
    
    ```bash
    ffuf -w wordlist.txt -u http://example.com/FUZZ -fs 1250
    ```
    
- Match by Status Code (-mc):
    
    To only show results with specific status codes (e.g., only show successful 200 OK responses).
    
    ```bash
    ffuf -w wordlist.txt -u http://example.com/FUZZ -mc 200
    ```
    

### 5. Recursive Fuzzing

To find directories within directories that have been discovered.

```bash
ffuf -w wordlist.txt -u http://example.com/FUZZ -recursion -recursion-depth 2
```

- **Explanation:** If `ffuf` finds a directory (e.g., `/admin`), it will start a new scan at `http://example.com/admin/FUZZ`. The `-recursion-depth` flag limits how deep it will go.
    

### 6. POST Request Fuzzing

Fuzzing for parameters in a POST request, such as a username field.

```bash
ffuf -w /path/to/users.txt -u http://example.com/login -X POST -d 'user=FUZZ&pass=password123' -H "Content-Type: application/x-www-form-urlencoded"
```

- **Explanation:**
    
    - `-X POST`: Specifies the request method.
        
    - `-d`: Sets the data for the POST request body. `FUZZ` is replaced with usernames from the wordlist.
        
    - `-H`: Adds a required header for form submissions.
        

### 7. Saving Output to a File

You can save the results for later analysis in various formats.

```bash
ffuf -w wordlist.txt -u http://example.com/FUZZ -o results.json -of json
```

- **Explanation:**
    
    - `-o`: Specifies the output file name.
        
    - `-of`: Specifies the output format (`json`, `html`, `csv`, etc.).
        

---
