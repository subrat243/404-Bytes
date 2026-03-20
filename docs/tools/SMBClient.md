A command-line utility for accessing SMB/CIFS resources on servers.

---

## Description

`smbclient` is a powerful and versatile tool, part of the Samba suite, that provides an "ftp-like" interface for connecting to Server Message Block (SMB) or Common Internet File System (CIFS) shares on a network. It can be used to list, browse, transfer files to and from, and interact with network shares on Windows and other Samba-enabled systems.

It can be run in two primary modes:

1. **Interactive Mode**: Where you connect to a share and receive a prompt (`smb: \>`) to execute commands like `ls`, `get`, and `put`.
    
2. **Non-Interactive Mode**: Where you execute a single command directly from the command line and exit.
    

---

## Installation

`smbclient` is typically part of the `samba-client` or `smbclient` package. You can install it using your system's package manager.

**For Debian/Ubuntu-based systems:**

Bash

```
sudo apt-get update && sudo apt-get install smbclient
```

**For RHEL/CentOS-based systems:**

Bash

```
sudo yum install samba-client
```

---

## Usage

The basic syntax for `smbclient` is as follows:

Bash

```
smbclient //SERVER/SHARE [options]
```

### Common Options

- `-L, --list=HOST`: List the shares available on a given host.
    
- `-U, --user=USERNAME`: Specifies the username to connect with. Often in the format `USERNAME%PASSWORD`.
    
- `-N, --no-pass`: Suppresses the normal password prompt. Useful for connecting to shares that do not require a password (null sessions).
    
- `-c, --command='COMMAND'`: Executes a single command and then terminates the connection.
    
- `-p, --port=PORT`: Specifies the TCP port number to connect to on the server (default is 445).
    
- `-I, --ip-address=IP_ADDRESS`: Specifies the IP address of the server to connect to.
    

---

## Examples

### 1. Listing Shares on a Server

You can list all visible shares on a server. This is often a first step in network enumeration.

Anonymous Listing (Null Session):

This command attempts to list shares without providing a username or password.

Bash

```
smbclient -L //192.168.1.10 -N
```

Authenticated Listing:

This command will prompt for the password for the specified user admin.

Bash

```
smbclient -L //192.168.1.10 -U admin
```

### 2. Connecting to a Share Interactively

To connect to a share named `Public` on a server and enter the interactive prompt:

Bash

```
smbclient //192.168.1.10/Public
```

Once connected, you will see a prompt like `smb: \>`. You can now use FTP-like commands.

- **List files and directories:**
    
    ```
    smb: \> ls
    ```
    
- **Download a file:**
    
    ```
    smb: \> get important_document.pdf
    ```
    
- **Upload a file:**
    
    ```
    smb: \> put local_report.docx
    ```
    
- **Navigate directories:**
    
    ```
    smb: \> cd Confidential
    ```
    
- **Exit the session:**
    
    ```
    smb: \> exit
    ```
    

### 3. Downloading a File Non-Interactively

To download the file `data.zip` from the `Backup` share without entering the interactive shell, you can use the `-c` option.

Bash

```
smbclient //192.168.1.10/Backup -U user%password123 -c 'get data.zip'
```

The file `data.zip` will be downloaded to your current local directory.

### 4. Uploading a File Non-Interactively

To upload a local file named `archive.tar` to the `Uploads` share:

Bash

```
smbclient //192.168.1.10/Uploads -U user%password123 -c 'put archive.tar'
```

This command will upload `archive.tar` from your current local directory to the root of the `Uploads` share.

---
