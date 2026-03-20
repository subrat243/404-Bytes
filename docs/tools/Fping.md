# fping - The High-Performance Pinger

`fping` is a command-line tool used to send ICMP Echo Request packets to network hosts, similar to `ping`, but with a focus on performance and scripting. Unlike the standard `ping` which tests a single host at a time, `fping` can ping multiple hosts in parallel. It is an essential tool for network administrators and security professionals for network discovery, monitoring, and diagnostics.

---

## Key Features

- **Parallel Pinging**: Can ping a large number of hosts simultaneously, making it much faster for scanning entire subnets.
    
- **Script-Friendly Output**: Provides various output formats that are easy to parse in shell scripts. It clearly indicates which hosts are up and which are down.
    
- **Flexible Host Input**: Accepts hosts from the command line, from a file, or as a specified IP range.
    
- **Customizable Parameters**: Allows users to configure the number of pings, interval between pings, timeout values, and packet size.
    

---

## Installation

### For Debian/Ubuntu-based Systems

You can install `fping` using the `apt` package manager.

Bash

```
sudo apt update
sudo apt install fping
```

### For RHEL/CentOS-based Systems

Use the `yum` or `dnf` package manager to install `fping`. You may need to enable the EPEL (Extra Packages for Enterprise Linux) repository first.

Bash

```
sudo yum install epel-release
sudo yum install fping
```

---

## Usage Examples

Below are common examples of how to use the `fping` command.

### 1. Ping a Single Host

To check if a single host is reachable, simply provide its IP address or hostname. The `-a` flag shows only systems that are alive.

Bash

```
fping -a 8.8.8.8
```

**Output:**

```
8.8.8.8 is alive
```

### 2. Ping Multiple Hosts

You can specify multiple hosts separated by spaces.

Bash

```
fping google.com 8.8.8.8 1.1.1.1
```

**Output:**

```
google.com is alive
1.1.1.1 is alive
8.8.8.8 is alive
```

### 3. Ping a Range of IP Addresses

Use the `-g` flag to generate a target list from a given IP range. This is useful for scanning a subnet.

Bash

```
fping -g 192.168.1.1 192.168.1.100
```

This command will ping every host from `192.168.1.1` to `192.168.1.100`. To show only the hosts that are up, combine it with `-a`.

Bash

```
fping -a -g 192.168.1.0/24
```

This example uses CIDR notation to scan the entire `/24` subnet.

### 4. Ping Hosts from a File

If you have a list of hosts in a text file (one per line), you can use fping to check all of them.

hosts.txt:

```
google.com
github.com
192.168.1.50
```

**Command:**

Bash

```
fping < hosts.txt
```

### 5. Customizing Ping Behavior

You can control the ping parameters using various flags:

- `-c <count>`: Number of pings to send to each target.
    
- `-i <milliseconds>`: Interval between pings to a single target (default is 25ms).
    
- `-t <milliseconds>`: Timeout for waiting for a reply (default is 500ms).
    

**Example:** Send 3 pings to each host with a 1-second interval.

Bash

```
fping -c 3 -i 1000 google.com github.com
```

### 6. Show Unreachable Hosts

To get a list of hosts that are not responding, use the `-u` flag.

Bash

```
fping -u 192.168.1.99 192.168.1.100
```

**Output:**

```
192.168.1.99 is unreachable
192.168.1.100 is unreachable
```