# Windows Privilege Escalation (THM)

Windows Privilege Escalation refers to the process of gaining higher-level permissions on a Windows system than those initially assigned. In most attack scenarios, an attacker begins with access as a low-privileged or standard user and attempts to elevate privileges to Administrator or SYSTEM level.

Privilege escalation is a critical phase of post-exploitation. Without elevated privileges, an attacker is limited in persistence, lateral movement, credential dumping, and system control. Most real-world privilege escalation scenarios arise due to misconfigurations, insecure permissions, poor credential handling, or outdated software rather than advanced exploits.
# Windows Account and Privilege Model

## Windows Account Types

### Standard User
Has limited access. Cannot modify system-wide settings or protected files.
### Administrator
Has full control over system configuration, services, and protected directories.
### SYSTEM / LocalSystem
Used internally by Windows. Has more privileges than administrators.
### Local Service
Used by services with minimal privileges and anonymous network access.
### Network Service
Used by services with minimal privileges but authenticates using machine credentials.

# Credential-Based Privilege Escalation Techniques

### Unattended Installation Credential Discovery

In enterprise environments, Windows is often installed using automated deployment mechanisms. These unattended installations require administrative credentials, which may be stored locally in configuration files.

If these files remain accessible after installation, they may expose administrator credentials in plaintext.

Common file locations include:

```
C:\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\System32\sysprep.inf
```

This technique is highly impactful because it can immediately provide administrative access without further exploitation.

---

### PowerShell History Credential Leakage

PowerShell stores command history for usability. If users or administrators execute commands containing passwords directly in the command line, those credentials are recorded in history files.

These files are often readable by the same user and sometimes by other users depending on permissions.

Relevant file location:

```
%userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

This technique frequently exposes credentials used for network access, service authentication, or administrative operations.

---

### Stored Windows Credentials Abuse

Windows provides functionality to store credentials for reuse, particularly for network authentication and administrative tasks.

Stored credentials can be enumerated using:

```cmd
cmdkey /list
```

Although passwords are not displayed, saved credentials can sometimes be reused using the `runas` command with the `/savecred` option, potentially allowing execution under a higher-privileged account.

---

### Application Configuration Credential Exposure

Applications often store credentials in configuration files. Web applications hosted on IIS commonly store database credentials in `web.config` files.

These credentials may be reused across systems or services, making them valuable targets during privilege escalation.

Typical locations include:

```
C:\inetpub\wwwroot\web.config
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

# Scheduled Task Privilege Escalation Techniques

Windows Scheduled Tasks allow automated execution of programs or scripts. If a scheduled task runs with elevated privileges and executes a file that is writable by a low-privileged user, privilege escalation becomes possible.

The attacker modifies the task’s executable or script to include malicious commands. When the task runs, the payload executes with the task’s configured privileges.

Task enumeration:

```cmd
schtasks /query /fo list /v
```

Permission verification:

```cmd
icacls C:\path\to\taskfile
```

This technique commonly results in execution as a service account or administrator.

# Windows Service Privilege Escalation Techniques

### Insecure Service Executable Permissions

Windows services execute binaries under privileged accounts. If the service executable is writable by standard users, it can be replaced with malicious code.

Upon service restart, the attacker’s payload executes with the service’s privileges, often SYSTEM.

This is one of the most severe misconfigurations encountered during Windows privilege escalation.

---

### Unquoted Service Path Vulnerability

If a service executable path contains spaces and is not enclosed in quotation marks, Windows may incorrectly parse the path and attempt to execute unintended binaries.

For example:

```
C:\MyPrograms\Disk Sorter Enterprise\bin\service.exe
```

Windows searches for executables in intermediate paths. If an attacker can write to one of those locations, they can hijack service execution.

This vulnerability is common when services are installed in non-default directories with weak permissions.

---

### Insecure Service Configuration Permissions (Service DACL)

Even when service binaries are secure, the service configuration itself may be writable.

If standard users have `SERVICE_ALL_ACCESS` permissions, they can reconfigure the service to:

- Change the executable path
    
- Change the service account
    
- Execute arbitrary binaries
    

This allows direct escalation to SYSTEM without modifying existing files.

# Privilege Abuse Techniques

Windows assigns specific privileges to users and groups beyond file permissions. Certain privileges can be abused to bypass security controls.

Privileges can be enumerated using:

```cmd
whoami /priv
```

---

### SeBackupPrivilege and SeRestorePrivilege Abuse

These privileges allow reading and writing files regardless of file permissions. They are commonly assigned to Backup Operators.

An attacker can use these privileges to extract the SAM and SYSTEM registry hives, obtain password hashes, and perform pass-the-hash attacks to gain SYSTEM access.

This technique bypasses traditional access control mechanisms entirely.

---

### SeTakeOwnershipPrivilege Abuse

SeTakeOwnershipPrivilege allows a user to take ownership of any object on the system.

Once ownership is obtained, the attacker can assign full permissions and modify protected files. A common exploitation method involves replacing `utilman.exe`, which executes as SYSTEM on the login screen, with `cmd.exe`.

Triggering the Ease of Access feature then provides a SYSTEM shell.

---

### SeImpersonatePrivilege and SeAssignPrimaryTokenPrivilege Abuse

These privileges allow processes to impersonate authenticated users.

Service accounts such as Network Service, Local Service, and IIS application pools commonly have these privileges.

Exploitation techniques such as RogueWinRM, PrintSpoofer, and similar attacks force privileged services to authenticate to attacker-controlled processes, resulting in SYSTEM-level code execution.

# Exploiting Vulnerable or Unpatched Software

Installed software may introduce local privilege escalation vulnerabilities due to insecure services, IPC mechanisms, or drivers.

Installed software can be enumerated using:

```cmd
wmic product get name,version
```

Identified software versions should be checked against vulnerability databases.

A notable example is Druva inSync, which exposed a SYSTEM-level RPC service allowing arbitrary command execution due to improper input validation.

Exploitation of such vulnerabilities often results in full administrative control.

# Automated Privilege Escalation Enumeration Tools

Automated tools assist in identifying privilege escalation vectors but should be used to complement manual analysis.

WinPEAS performs extensive enumeration of services, permissions, registry keys, credentials, and system configurations.

PrivescCheck is a PowerShell-based enumeration tool suitable for environments where binaries are restricted.

WES-NG identifies missing Windows patches using offline analysis of `systeminfo` output.

Metasploit’s local exploit suggester identifies known privilege escalation exploits when a Meterpreter session is available.

# Privilege Escalation Methodology

A systematic approach to Windows privilege escalation includes:

- Identifying current user context and group memberships
    
- Enumerating privileges and credentials
    
- Inspecting scheduled tasks and services
    
- Analyzing file and service permissions
    
- Enumerating installed software
    
- Using automated tools for confirmation
    
- Carefully validating exploitability
    

# Conclusion

Windows privilege escalation is primarily driven by misconfigurations, insecure defaults, and operational oversights rather than advanced exploitation techniques. Effective escalation requires deep understanding of Windows internals, careful enumeration, and disciplined exploitation.

Manual analysis remains the most reliable method, with automated tools serving as supportive aids rather than replacements.

---
