# Introduction

Command Injection is a critical web application vulnerability where an attacker is able to execute unintended operating system (OS) commands through an application. This occurs when an application includes user-controlled input directly into an OS command without proper validation, filtering, or sanitization.

This vulnerability is highly dangerous because it allows an attacker to interact directly with the underlying system using the permissions of the running application. For example, if a web application runs as a user named `joe`, any injected commands will execute with the same permissions and access rights as user `joe`.

Command Injection may also be referred to as Remote Code Execution (RCE) because the attacker can remotely execute system commands. This is a high-impact vulnerability that may lead to unauthorized file access, data exfiltration, privilege escalation, persistent backdoors, system takeover, and potentially full infrastructure compromise.

This topic covers:

- Understanding how Command Injection works
    
- Identifying when a web application is vulnerable
    
- Testing and exploitation techniques
    
- Example payloads for both Linux and Windows environments
    
- Remediation strategies
    

---

# What Is Command Injection

Command Injection is the exploitation of insecure application functionality that interacts directly with the operating system using unsanitized user input. The attacker alters or extends the executed OS command by injecting additional unexpected commands using shell syntax, gaining arbitrary command execution capability.

Example goal:  
If the attacker is able to inject `whoami` and obtain output such as `www-data`, it confirms that OS command execution is possible.

Attackers may also use it to access sensitive files, such as:

- `/etc/passwd`
    
- `/etc/shadow`
    
- Database credentials
    
- SSH keys
    
- Source code
    
- System logs
    

Command Injection has consistently ranked as a high-risk web security vulnerability according to security reports from Contrast Security, and it appears in the OWASP Top 10 list under Injection vulnerabilities.

---

# Why Applications Become Vulnerable

Some programming languages provide built-in functions to execute system commands for automation purposes. When a developer directly concatenates user input into these functions, the attacker can manipulate the command to execute arbitrary instructions.

Languages and frameworks where this risk commonly appears include:

- PHP (`system`, `exec`, `shell_exec`, `passthru`)
    
- Python (`os.system`, `subprocess.Popen` with shell=True)
    
- NodeJS (`child_process.exec`)
    
- Ruby (`system`, backticks, `%x`)
    

Other contributing causes include:

- Poor or no input validation
    
- Over-privileged application execution
    
- Failure to use safe APIs
    
- Lack of output encoding
    
- Taking shortcuts in application logic
    

---

# Vulnerable Example Explanation (PHP)

The following code accepts user input from a GET request parameter called `title` and executes a `grep` search command using the provided value:

```php
<?php
$songs = "/var/www/html/songs";                 // 1. Local directory storing song data

if (isset($_GET["title"])) {                    // 2. Reads user input from GET request
    $title = $_GET["title"];

    $command = "grep $title /var/www/html/songtitle.txt"; // 3. Input is inserted directly into OS command

    $result = exec($command);                   // 4. Executes command and returns output

    if ($result == "") {
        echo "<p>The requested song</p><p>$title does not exist</p>";
    } else {
        echo "<p>The requested song</p><p>$title exists</p>";
    }
}
?>
```

### Why It Is Vulnerable

The key problem is that user-supplied data (`$title`) is directly added to a shell command. This means an attacker can append shell operators like:

```
;  &&  |  ||  &  `command`  $(command)
```

### Example Attack

URL:

```
http://target.com/search.php?title=Beatles;cat /etc/passwd
```

Resulting OS command:

```
grep Beatles /var/www/html/songtitle.txt;cat /etc/passwd
```

The second command prints sensitive system information.

---

# Vulnerable Example Explanation (Python Flask)

```python
import subprocess
from flask import Flask

app = Flask(__name__)

def execute_command(shell):
    return subprocess.Popen(shell, shell=True, stdout=subprocess.PIPE).stdout.read()

@app.route('/<shell>')
def run(shell):
    return execute_command(shell)
```

Here, any input passed through the URL path becomes a shell command. For example:

```
http://flaskapp.thm/whoami
```

This design grants the user direct command execution capability.

---

# Exploiting Command Injection

Applications that execute system commands using user input can often be exploited using shell chaining operators.

Common operator examples:

|Operator|Meaning|
|---|---|
|`;`|Execute next command regardless of previous result|
|`&&`|Execute next only if previous succeeds|
|`||
|`|`|
|`&`|Run in background|

---

# Types of Command Injection

|Type|Description|
|---|---|
|Verbose|Output is visible directly from the application|
|Blind|No visible output; relies on timing or stored output|

---

# Detecting Blind Command Injection

Blind Command Injection does not display output directly. Instead, attackers use observable side effects such as delays or writing to a file.

Testing methods:

1. Use sleep or ping-based delays  
    Example:
    
    ```
    test; sleep 10
    ```
    
    or
    
    ```
    test; ping -c 5 127.0.0.1
    ```
    
2. Write output to a file and then retrieve
    
    ```
    test; whoami > /tmp/output.txt
    ```
    
    Then:
    
    ```
    test; cat /tmp/output.txt
    ```
    
3. Use `curl` to test remotely
    
    ```
    curl "http://vulnerable.app/process.php?search=Beatles;whoami"
    ```
    

---

# Useful Payloads

### Linux

|Payload|Description|
|---|---|
|`whoami`|Determine execution context|
|`ls`|List files in current directory|
|`ping`|Useful for delay-based blind detection|
|`sleep`|Blind delay when ping is unavailable|
|`nc`|Spawn reverse shell for remote access|

### Windows

|Payload|Description|
|---|---|
|`whoami`|Determine current user|
|`dir`|List directory contents|
|`ping`|Blind injection detection|
|`timeout`|Delay injection alternative|

---

# Remediation and Prevention

### 1. Avoid Vulnerable Functions

In PHP, the following functions should not be used with untrusted input:

- `exec`
    
- `system`
    
- `passthru`
    
- `shell_exec`
    

### 2. Enforce Input Validation

Use strict whitelist validation instead of blacklist filtering.

Example: Accept only numbers

```php
if (!preg_match('/^[0-9]+$/', $_GET['input'])) {
    die("Invalid input");
}
```

### 3. Use Filtered Input Functions

Example using `filter_input()`:

```php
$number = filter_input(INPUT_GET, "num", FILTER_VALIDATE_INT);
if ($number === false) {
    die("Invalid input");
}
```

### 4. Avoid Shell Usage When Possible

Use safe programming functions or built-in APIs instead of executing system commands.

Example alternative:  
Replace:

```
system("grep $title file.txt");
```

Use:

```
$lines = file("file.txt", FILE_IGNORE_NEW_LINES);
in_array($title, $lines);
```

---

# Bypassing Filters

Even when filters are applied, attackers may circumvent them using encoding methods, whitespace tricks, or syntax variations.

Examples:

- Hex encoded characters
    
- URL encoding
    
- Using `${IFS}` in place of spaces
    
- Command substitution using backticks or `$()`
    

Example:

```
cat${IFS}/etc/passwd
```

---

# Summary

Command Injection occurs when user input is executed as part of a system command. When exploited, it can allow attackers to execute arbitrary commands, read sensitive files, escalate privileges, or obtain full system access. Identifying and preventing this vulnerability requires secure coding practices, strict input validation, safe API usage, and limited OS command execution.

For more advanced payload examples or bypass techniques, refer to publicly available payload repositories and security cheat sheets.

---
