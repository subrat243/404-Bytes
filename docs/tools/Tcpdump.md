# Tcpdump

### **What is `tcpdump`?**

`tcpdump` is a powerful command-line packet analyzer tool used to capture or filter network packets in real-time on Unix-like systems.

---

### **Interface and Capture Options**

##### `ip a s`

- Lists all network interfaces with IPs – helps identify which interface to capture from.
    

##### `tcpdump`

- Starts packet capture on the default interface (usually `eth0` or `wlan0`).
    

##### `tcpdump -i INTERFACE`

- Captures packets from a specific interface.
    

#### Example:

```bash
tcpdump -i eth0
```

Captures packets from the `eth0` interface.

##### `tcpdump -i any`

- Captures packets from all interfaces.
    

#### Example:

```bash
tcpdump -i any
```

Useful when you're unsure which interface will receive the packet.

---

### **Common Options**

|Option|Description|
|---|---|
|`-w FILE`|Write output to a file in `.pcap` format|
|`-r FILE`|Read packets from a `.pcap` file|
|`-c COUNT`|Stop after capturing a number of packets|
|`-n`|Don’t resolve IP addresses to domain names|
|`-nn`|Don’t resolve IPs or port names|
|`-v`, `-vv`, `-vvv`|Verbose output; shows more header fields|

#### Example:

```bash
tcpdump -i eth0 -c 10 -nn
```

Capture 10 packets from `eth0`, showing IPs and port numbers in numeric form.

---

### **Filters**

##### Host-based Filters

|Command|Description|
|---|---|
|`tcpdump host 192.168.1.1`|Captures packets to/from IP|
|`tcpdump src host 10.0.0.1`|Packets where 10.0.0.1 is the source|
|`tcpdump dst host 10.0.0.1`|Packets where 10.0.0.1 is destination|

---

##### Port-based Filters

|Command|Description|
|---|---|
|`tcpdump port 80`|Packets to/from port 80|
|`tcpdump src port 22`|Packets from source port 22|
|`tcpdump dst port 443`|Packets to destination port 443|

---

##### Protocol-based Filters

|Command|Description|
|---|---|
|`tcpdump icmp`|Captures ICMP packets (e.g., ping)|
|`tcpdump tcp`|Captures only TCP packets|
|`tcpdump udp`|Captures only UDP packets|

#### Example:

```bash
tcpdump udp or icmp
```

Captures both UDP and ICMP traffic.

---

##### Logical Operators

- `and`: Combine two filters
    
- `or`: Match if either condition is true
    
- `not`: Negate a condition
    

#### Examples:

```bash
tcpdump tcp and port 80
tcpdump host 192.168.0.1 or host 192.168.0.2
tcpdump not tcp
```

---

### **Packet Length Filters**

- `greater LENGTH`: Packet size ≥ LENGTH
    
- `less LENGTH`: Packet size ≤ LENGTH
    

#### Example:

```bash
tcpdump greater 1000
```

Captures packets larger than 1000 bytes.

---

### **Advanced Filtering with Bitwise Operators**

Tcpdump supports filters using byte offsets and bitwise operations.

##### Format: `proto[expr:size]`

- `proto`: Protocol (`ip`, `tcp`, `ether`)
    
- `expr`: Byte offset
    
- `size`: 1, 2, or 4 (optional)
    

#### Example 1:

```bash
tcpdump 'ether[0] & 1 != 0'
```

Matches packets sent to multicast Ethernet addresses.

#### Example 2:

```bash
tcpdump 'ip[0] & 0xf != 5'
```

Captures IP packets with IP header options set.

---

### **TCP Flags Filtering**

TCP flags field is accessible using `tcp[tcpflags]`.

|Flag|Description|
|---|---|
|`tcp-syn`|Connection start|
|`tcp-ack`|Acknowledgment|
|`tcp-fin`|Finish connection|
|`tcp-rst`|Reset connection|
|`tcp-push`|Push data|

#### Examples:

```bash
tcpdump 'tcp[tcpflags] == tcp-syn'
```

Only SYN flag is set – beginning of TCP handshake.

```bash
tcpdump 'tcp[tcpflags] & tcp-syn != 0'
```

SYN flag is set (others may also be set).

```bash
tcpdump 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'
```

At least SYN or ACK flag is set.

---

### **Output Formatting Options**

|Option|Description|
|---|---|
|`-q`|Quick output (brief info)|
|`-e`|Show link-level header|
|`-A`|Print packet contents in ASCII|
|`-xx`|Show packet in hex|
|`-X`|Print hex + ASCII (good for forensic analysis)|

#### Example:

```bash
tcpdump -i eth0 -c 5 -X
```

Captures 5 packets and shows headers + data in hex and ASCII.

---

### **Writing and Reading Pcap Files**

- **Write to file:**
    

```bash
tcpdump -i eth0 -w capture.pcap
```

- **Read from file:**
    

```bash
tcpdump -r capture.pcap
```

This is useful for analyzing traffic later using tools like Wireshark.

---

### **Real-World Use Cases**

##### 1. Capture HTTP traffic:

```bash
tcpdump -i eth0 tcp port 80
```

##### 2. Monitor ping (ICMP):

```bash
tcpdump -i wlan0 icmp
```

##### 3. Detect traceroute (ICMP time-exceeded):

```bash
tcpdump icmp and 'icmp[0] == 11'
```

##### 4. Capture TCP handshake packets:

```bash
tcpdump 'tcp[tcpflags] & tcp-syn != 0'
```

---

### **Tips**

- Use `-nn` to speed up capture by avoiding DNS resolution.
    
- Run with `sudo` to access all interfaces and packets.
    
- Combine with `grep` to search live output:
    

```bash
tcpdump -i any -nn | grep "192.168.1.5"
```

---