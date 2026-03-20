# RPCClient

`rpcclient` is a versatile command-line utility, part of the Samba suite, used to execute Microsoft Remote Procedure Call (MSRPC) functions against Windows servers. It is an essential tool for system administrators and security professionals for enumerating information from Windows hosts over the network.

---

## What is rpcclient?

At its core, `rpcclient` allows a user on a Linux/Unix-like system to interact with Windows machines by making remote procedure calls. This is similar to how Windows clients communicate with servers for various tasks like user management, service control, and share enumeration. It typically communicates over SMB (Server Message Block) on port 445/TCP.

---

## Installation

`rpcclient` is included in the `samba-common-bin` package on Debian-based systems (like Ubuntu) or the `samba-client` package on Red Hat-based systems (like CentOS/Fedora).

**For Debian/Ubuntu:**

Bash

```
sudo apt-get update
sudo apt-get install samba-common-bin
```

**For CentOS/Fedora:**

Bash

```
sudo yum install samba-client
```

---

## Usage and Examples

### Basic Syntax

The general syntax for connecting with `rpcclient` is:

Bash

```
rpcclient -U [domain/]username%password [target_ip]
```

### Authentication Methods

- **With a Password:**
    
    Bash
    
    ```
    rpcclient -U 'WORKGROUP/administrator%Password123' 192.168.1.100
    ```
    
- **With NTLM Hash (Pass-the-Hash):**
    
    Bash
    
    ```
    rpcclient -U 'administrator' --pw-nt-hash 'aad3b435b51404eeaad3b435b51404ee' 192.168.1.100
    ```
    
- **Anonymous / Null Session:** If the server allows it, you can connect without credentials.
    
    Bash
    
    ```
    rpcclient -U "" -N 192.168.1.100
    ```
    
    The `-N` flag tells the client not to ask for a password.
    

### Interactive Shell

Once connected, `rpcclient` provides an interactive shell where you can execute various commands.

Bash

```
rpcclient $>
```

### Common Commands

Here are some of the most useful commands available within the `rpcclient` interactive shell:

|Command|Description|
|---|---|
|`srvinfo`|Displays server information like OS version and server name.|
|`enumusers`|Enumerates all users on the target system.|
|`queryuser <username_or_rid>`|Retrieves detailed information about a specific user.|
|`netshareenumall`|Lists all available network shares on the target.|
|`lookupsids <SID>`|Resolves a Security Identifier (SID) to a username.|
|`lsaquery`|Queries LSA (Local Security Authority) policies.|
|`enumprinters`|Lists available printers.|
|`help`|Shows a list of available commands.|
|`quit` or `exit`|Exits the interactive shell.|

---

## Example Scenario

Let's demonstrate a typical enumeration workflow on a target machine at `10.10.10.5` with a null session.

**1. Connect to the target machine.**

Bash

```
rpcclient -U "" -N 10.10.10.5
```

**2. Enumerate users.**

Bash

```
rpcclient $> enumusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[DefaultAccount] rid:[0x1f6]
user:[testuser] rid:[0x3e9]
```

3. Get detailed information for a specific user, "testuser".

The RID for "testuser" is 0x3e9.

Bash

```
rpcclient $> queryuser 0x3e9
User Name   : testuser
Full Name   :
Home Drive  :
Dir Drive   :
Profile Path:
Logon Script:
Description : Test User Account
Workstations:
Comment     :
User Comment:
Password last set: 10/5/2025 7:10:45 PM
Last logon: 0
...and more details
```

**4. Enumerate network shares.**

Bash

```
rpcclient $> netshareenumall
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
SharedFolder    Disk      General Purpose Share
```

**5. Exit the session.**

Bash

```
rpcclient $> exit
```

---
