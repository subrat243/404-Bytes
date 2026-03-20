# Introduction to HTTP and cURL

Web applications communicate using **HTTP (HyperText Transfer Protocol)**.  
Whenever a user opens a website, the browser sends an **HTTP request**, and the server replies with an **HTTP response**.

### What is cURL?

`cURL` (Client URL) is a **command-line tool** used to:

- Send HTTP requests
    
- Test web applications
    
- Debug APIs
    
- Perform security testing
    

It allows interaction with a web server **without using a browser**.

---

# Basic HTTP Request Using cURL

### Example:

`curl http://MACHINE_IP/`

### Explanation:

- Sends an **HTTP GET request**
    
- Fetches the webpage content
    
- Displays raw HTML in the terminal
    

This is useful for:

- Checking if a server is alive
    
- Viewing page source
    
- Testing endpoints
    

---

# Sending POST Requests

### Example:

`curl -X POST -d "username=user&password=user" http://MACHINE_IP/post.php`

### Explanation:

|Option|Meaning|
|---|---|
|`-X POST`|Specifies POST method|
|`-d`|Sends data in request body|
|`username=user`|Form field|
|`password=user`|Form field|

Used for:

- Login testing
    
- Form submission
    
- Authentication testing
    

---

# Viewing Server Response Headers

### Example:

`curl -i -X POST -d "username=user&password=user" http://MACHINE_IP/post.php`

### Explanation:

- `-i` shows:
    
    - HTTP status code
        
    - Cookies
        
    - Headers
        
- Useful to check:
    
    - Session cookies
        
    - Redirects
        
    - Authentication responses
        

---

# Cookies and Session Handling

Web apps use **cookies** to maintain login sessions.

### Step 1: Save Cookies

`curl -c cookies.txt -d "username=admin&password=admin" http://MACHINE_IP/session.php`

- Stores cookies in `cookies.txt`

---

### Step 2: Reuse Cookies

`curl -b cookies.txt http://MACHINE_IP/session.php`

- Reuses session  
- Simulates logged-in user  
- Useful for session hijacking tests

---

# Automating Login (Brute Force Simulation)

### Password File:

`admin123 password letmein secret`

### Bash Script:

`for pass in $(cat passwords.txt); do   echo "Trying password: $pass"   response=$(curl -s -X POST -d "username=admin&password=$pass" http://MACHINE_IP/bruteforce.php)   if echo "$response" | grep -q "Welcome"; then     echo "[+] Password found: $pass"     break   fi done`

### Explanation:

- Reads passwords one by one
    
- Sends POST request
    
- Checks response
    
- Stops when login succeeds
    

**This logic is used by tools like:**

- Hydra
    
- Burp Intruder
    
- WFuzz
    

---

# User-Agent Bypass

Some websites block tools like curl by checking the **User-Agent**.

### Example:

`curl -A "internalcomputer" http://MACHINE_IP/ua_check.php`

### Why it works:

- `-A` sets a fake browser name
    
- Bypasses simple bot detection
    
- Mimics real browsers
    

---

# Real-World Security Relevance

This technique is used in:

- Web application testing  
- API security testing  
- Authentication bypass analysis  
- Session hijacking  
- Brute force simulation  
- Red team operations

---

# Summary

- cURL allows **direct interaction with web servers**
    
- Useful for:
    
    - GET & POST requests
        
    - Session handling
        
    - Brute force testing
        
    - User-Agent bypass
        
- Forms the foundation of **web exploitation tools**
    
- Essential skill for **penetration testers & SOC analysts**