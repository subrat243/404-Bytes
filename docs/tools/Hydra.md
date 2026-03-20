### 1. What is Hydra?

- **Hydra** is a **brute force online password cracking tool**.
    
- It automates the process of guessing login credentials for various services.
    
- Supports **dozens of protocols**: SSH, FTP, HTTP(S), RDP, MySQL, PostgreSQL, SMB, SNMP, Telnet, etc.
    
- Widely used by **penetration testers and red teamers** to test authentication security.
    

**Why important?**  
Weak or default passwords (`admin:password`, `123456`, etc.) are very common. Hydra shows how quickly these can be cracked if not secured.

---

### 2. Installing Hydra

- **Ubuntu/Debian** → `sudo apt install hydra`
    
- **Fedora** → `sudo dnf install hydra`
    
- From source → [Official Repo](https://github.com/vanhauser-thc/thc-hydra)
    

---

### 3. Hydra Syntax

```
hydra [options] [target] [service/protocol]
```

##### Common Options:

|Option|Meaning|
|---|---|
|`-l`|Single username to test|
|`-L`|File with multiple usernames|
|`-p`|Single password to test|
|`-P`|File with multiple passwords (wordlist)|
|`-t`|Number of threads (parallel attempts)|
|`-V`|Verbose mode – shows each attempt|
|`-f`|Exit after the first valid login found|

---

### 4. Example: Brute Force FTP Login

```
hydra -l user -P passlist.txt ftp://MACHINE_IP
```

**Explanation**:

- `-l user` → try login with username `user`
    
- `-P passlist.txt` → test all passwords from file
    
- `ftp://MACHINE_IP` → target protocol and IP
    

Hydra will stop when it finds the correct password.

---

### 5. Example: Brute Force SSH Login

```
hydra -l root -P passwords.txt MACHINE_IP -t 4 ssh
```

**Explanation**:

- `-l root` → username = `root`
    
- `-P passwords.txt` → password list
    
- `-t 4` → run 4 threads in parallel (faster cracking)
    
- `ssh` → protocol
    

Hydra will try logging into SSH service of the target machine using every password.

---

### 6. Example: Brute Force Web Form (POST Request)

```
hydra -l admin -P wordlist.txt MACHINE_IP http-post-form "/login.php:username=^USER^&password=^PASS^:F=incorrect" -V
```

**Explanation**:

- `/login.php` → path to login page
    
- `username=^USER^&password=^PASS^` → form fields (`^USER^` and `^PASS^` get replaced with values from Hydra)
    
- `F=incorrect` → string from response indicating login **failed**
    
- `-V` → show each attempt
    

Hydra will brute force the form and stop when login is successful.

---

### 7. Other Supported Protocol Examples

##### SMB

```
hydra -L users.txt -P passwords.txt smb://MACHINE_IP
```

##### MySQL

```
hydra -l root -P db_passlist.txt mysql://MACHINE_IP
```

##### RDP

```
hydra -L users.txt -P passwords.txt rdp://MACHINE_IP
```

---

### 8. Security Implications

- **Weak passwords** are highly vulnerable.
    
- Hydra demonstrates why:
    
    - Default credentials (`admin:password`)
        
    - Short passwords (<8 chars)
        
    - Lack of complexity (no special chars/numbers)
        
- Organizations should enforce:
    
    - Strong password policies
        
    - Multi-Factor Authentication (MFA)
        
    - Account lockout after multiple failed attempts
        

---

### 9. Key Takeaways

1. Hydra is a **powerful password brute forcing tool**.
    
2. Supports a wide range of **protocols & services**.
    
3. Can be used for:
    
    - Testing SSH/FTP logins
        
    - Cracking web form logins
        
    - Checking database authentication
        
4. Highlights the **importance of strong passwords** and **avoiding defaults**.
    

---