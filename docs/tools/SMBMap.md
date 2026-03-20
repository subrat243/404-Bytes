SMBMap is a powerful and versatile tool used for enumerating Server Message Block (SMB) share drives across an entire domain. It is a valuable asset for penetration testers, red teamers, and system administrators to discover and map accessible network shares, check permissions, and interact with the file systems.

---

## Key Features

- **Share Enumeration**: List accessible shares on a single host, a list of hosts, or an entire subnet.
    
- **Permission Checking**: Automatically enumerates read/write permissions for all discovered shares.
    
- **Recursive Listing**: List all files and directories within a specific share.
    
- **File Searching**: Search for specific filenames across all accessible shares, which is useful for finding sensitive files like configuration files or password lists.
    
- **File Operations**: Supports downloading and uploading files to and from target shares.
    
- **Command Execution**: Can execute commands on the target system if sufficient privileges are available.
    
- **Flexible Authentication**: Supports authentication with a username and password, pass-the-hash (LM:NT), and Kerberos.
    

---

## Usage and Examples

The basic syntax for SMBMap involves specifying a target host and authentication credentials.

### Basic Syntax

Bash

```
smbmap -H <target_ip> -u <username> -p <password> -d <domain>
```

---

### Examples

#### **1. Enumerate Shares on a Single Host**

This command will attempt to enumerate all shares on the specified host using the provided credentials. It will display the shares and their corresponding permissions (READ ONLY or READ/WRITE).

Bash

```
smbmap -H 192.168.1.10 -u Administrator -p 'Password123!' -d 'WORKGROUP'
```

#### **2. Enumerate Shares Using Pass-the-Hash**

If you have the NTLM hash of a user's password instead of the cleartext password, you can use it for authentication.

Bash

```
smbmap -H 192.168.1.10 -u Administrator -p 'aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c'
```

#### **3. Recursively List Contents of a Share**

To view all files and subdirectories within a specific share (e.g., `C$`), use the `-R` flag.

Bash

```
smbmap -H 192.168.1.10 -u Administrator -p 'Password123!' -R 'C$'
```

#### **4. Search for a Specific File**

To search for a file by name across all accessible shares on a host, use the `-f` flag. This is extremely useful for finding configuration files or files containing credentials.

Bash

```
smbmap -H 192.168.1.10 -u Administrator -p 'Password123!' -f 'unattend.xml'
```

#### **5. Download a File from a Share**

Use the `--download` option followed by the full path of the file within the share.

Bash

```
smbmap -H 192.168.1.10 -u Administrator -p 'Password123!' --download 'C$\Users\Administrator\Desktop\secret.txt'
```

The file will be downloaded to your current working directory.

#### **6. Execute a Command on the Target**

If the provided user has sufficient privileges, you can execute commands on the remote system using the `-x` flag.

Bash

```
smbmap -H 192.168.1.10 -u Administrator -p 'Password123!' -x 'whoami'
```

---
