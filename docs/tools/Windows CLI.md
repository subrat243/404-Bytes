###  1. System Information and Environment Management

#####  `set`

- **Description**: Displays, sets, or removes environment variables.
    
- **Use Case**: View system variables like `PATH`, `USERNAME`, `TEMP`.
    
- **Example**:
    
    ```cmd
    set
    set PATH
    ```
    
    _(Lists all or a specific environment variable)_
    

---

#####  `ver`

- **Description**: Displays the current version of Windows.
    
- **Use Case**: Verify OS version.
    
- **Example**:
    
    ```cmd
    ver
    ```
    
    _(Example Output: `Microsoft Windows [Version 10.0.19045.3930]`)_
    

---

#####  `systeminfo`

- **Description**: Displays detailed configuration and system info.
    
- **Use Case**: Get specs like BIOS version, RAM, domain, hotfixes.
    
- **Example**:
    
    ```cmd
    systeminfo
    systeminfo | more
    ```
    

---

#####  `help`

- **Description**: Lists all available internal commands and syntax.
    
- **Use Case**: Learn about a command’s usage quickly.
    
- **Example**:
    
    ```cmd
    help
    help dir
    ```
    

---

##### `cls`

- **Description**: Clears the terminal screen.
    
- **Use Case**: Clean up messy output before new commands.
    
- **Example**:
    
    ```cmd
    cls
    ```
    

---

### 2. Network Commands

##### `ipconfig`

- **Description**: Displays IP configuration for network interfaces.
    
- **Use Case**: Troubleshoot network connectivity.
    
- **Example**:
    
    ```cmd
    ipconfig
    ipconfig /all
    ```
    

---

##### `ping`

- **Description**: Sends ICMP packets to test connectivity.
    
- **Use Case**: Check if a host is reachable.
    
- **Example**:
    
    ```cmd
    ping google.com
    ping 8.8.8.8 -t
    ```
    

---

##### `tracert`

- **Description**: Traces the route to a network host.
    
- **Use Case**: Identify network bottlenecks or latency.
    
- **Example**:
    
    ```cmd
    tracert google.com
    ```
    

---

##### `nslookup`

- **Description**: Queries DNS servers.
    
- **Use Case**: Check domain name resolution or DNS configuration.
    
- **Example**:
    
    ```cmd
    nslookup facebook.com
    ```
    

---

##### `netstat`

- **Description**: Displays current TCP/UDP connections and ports.
    
- **Use Case**: Analyze active connections or detect malware communication.
    
- **Options**:
    
    - `-a`: Show all active connections and listening ports.
        
    - `-b`: Show executable responsible for each connection.
        
    - `-n`: Use numeric IPs and ports.
        
    - `-o`: Display the Process ID (PID).
        
    - `-h`: Display help.
        
- **Examples**:
    
    ```cmd
    netstat -a
    netstat -b
    netstat -an
    netstat -o
    ```
    

---

### 3. Directory & File Operations

##### `cd` (Change Directory)

- **Description**: Navigate between directories.
    
- **Example**:
    
    ```cmd
    cd C:\Users\Subrat\Documents
    cd ..
    ```
    

---

##### `dir`

- **Description**: Lists contents of the current directory.
    
- **Options**:
    
    - `/a`: Shows hidden and system files.
        
    - `/s`: Shows contents recursively.
        
- **Example**:
    
    ```cmd
    dir
    dir /a
    dir /s
    ```
    

---

##### `tree`

- **Description**: Graphically displays folder structure.
    
- **Use Case**: Visualize nested folders.
    
- **Example**:
    
    ```cmd
    tree
    tree D:\Projects
    ```
    

---

##### `mkdir` / `md`

- **Description**: Creates a new folder.
    
- **Example**:
    
    ```cmd
    mkdir Reports2025
    ```
    

---

##### `rmdir` / `rd`

- **Description**: Deletes a folder (only if it's empty).
    
- **Example**:
    
    ```cmd
    rmdir OldBackups
    ```
    

---

##### `type`

- **Description**: Displays content of text files.
    
- **Example**:
    
    ```cmd
    type notes.txt
    type logs.txt | more
    ```
    

---

##### `move`

- **Description**: Moves files/folders to another location.
    
- **Example**:
    
    ```cmd
    move *.txt D:\TextFiles\
    ```
    

---

##### `del` or `erase`

- **Description**: Deletes files.
    
- **Use Case**: Delete temporary or outdated files.
    
- **Example**:
    
    ```cmd
    del temp.txt
    erase *.log
    ```
    

---

##### `copy` (with wildcard)

- **Description**: Copies files, can use wildcards (`*`) for batch copying.
    
- **Example**:
    
    ```cmd
    copy *.md C:\Markdown\
    ```
    

---

### 4. Task and Process Management

##### `tasklist`

- **Description**: Lists all running processes.
    
- **Use Case**: Identify running tasks, check memory usage.
    
- **Filter Syntax**:
    
    ```cmd
    tasklist /FI "imagename eq sshd.exe"
    ```
    

---

##### `taskkill`

- **Description**: Terminates a running process using PID or image name.
    
- **Example**:
    
    ```cmd
    taskkill /PID 4567
    taskkill /IM notepad.exe /F
    ```
    

---

### 5. System Health & Diagnostics

##### `chkdsk`

- **Description**: Checks disk for file system errors and bad sectors.
    
- **Example**:
    
    ```cmd
    chkdsk C:
    ```
    

---

##### `driverquery`

- **Description**: Lists all installed device drivers.
    
- **Example**:
    
    ```cmd
    driverquery
    ```
    

---

##### `sfc /scannow`

- **Description**: Scans and repairs corrupted system files.
    
- **Example**:
    
    ```cmd
    sfc /scannow
    ```
    

---

### 6. Shutdown & Restart Management

##### `shutdown`

- **Purpose**: Shuts down, restarts, logs off, or hibernates the system.
    

|Command|Description|
|---|---|
|`shutdown /s`|Shutdown system|
|`shutdown /r`|Restart system|
|`shutdown /h`|Hibernate system|
|`shutdown /l`|Log off current user|
|`shutdown /a`|Abort scheduled shutdown|

- **Example**:
    
    ```cmd
    shutdown /s /t 60
    shutdown /a
    ```
    

---
