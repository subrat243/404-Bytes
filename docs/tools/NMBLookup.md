# NMBLookup

 `nmblookup` is a command-line tool for querying NetBIOS names and mapping them to IP addresses on a network. It is part of the Samba suite.

---

## Description

The `nmblookup` utility is used to query NetBIOS name information from name servers. It operates by sending NetBIOS over TCP/IP (NBT) queries to hosts on a network, allowing you to resolve NetBIOS names to IP addresses in a similar way that `nslookup` is used for DNS name resolution. This is particularly useful in environments with Windows machines or Samba servers for discovery and diagnostics.

---

## Usage

The basic syntax for the `nmblookup` command is:

Bash

```
nmblookup [options] name
```

- **`[options]`**: Flags that modify the command's behavior.
    
- **`name`**: The NetBIOS name you want to query.
    

---

## Common Options

- **`-A <ip_address>`**: Interprets the `name` as an IP address and performs a node status query on this address.
    
- **`-M`**: Searches for a master browser by looking up the NetBIOS name `__MSBROWSE__`.
    
- **`-R`**: Sets the recursion desired bit in the packet. This instructs the name server to perform a recursive query if it doesn't have the information.
    
- **`-S`**: After a name query has returned an IP address, this option performs a node status query as well. This provides a list of NetBIOS names registered by the host.
    
- **`-T`**: This will translate the IP addresses found into host names via a reverse DNS lookup.
    

---

## Examples

### 1. Find the IP Address of a Host

To find the IP address of a computer with the NetBIOS name "SERVER1".

Bash

```
nmblookup SERVER1
```

**Example Output:**

```
Querying SERVER1 on 192.168.1.255
192.168.1.15 SERVER1<00>
```

This indicates that the IP address for "SERVER1" is `192.168.1.15`.

### 2. Find the NetBIOS Name from an IP Address

To find the NetBIOS name associated with a specific IP address, use the `-A` option.

Bash

```
nmblookup -A 192.168.1.15
```

**Example Output:**

```
Looking up status of 192.168.1.15
        SERVER1         <00> -         B <ACTIVE>
        WORKGROUP       <00> - <GROUP> B <ACTIVE>
        SERVER1         <20> -         B <ACTIVE>

        MAC Address = 00-1A-2B-3C-4D-5E
```

This shows the names registered by the host at that IP, including its computer name ("SERVER1") and workgroup ("WORKGROUP").

### 3. Find the Master Browser for a Workgroup

To locate the master browser for the current workgroup.

Bash

```
nmblookup -M -
```

If you want to find the master browser for a specific workgroup, for example "MYGROUP":

Bash

```
nmblookup -M MYGROUP
```

**Example Output:**

```
querying MYGROUP on 192.168.1.255
192.168.1.10 MYGROUP<1d>
```

This output shows that the host at `192.168.1.10` is the master browser for the "MYGROUP" workgroup.

---
