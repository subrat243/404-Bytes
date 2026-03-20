# Wireshark

#### What is Wireshark?

**Wireshark** is a GUI-based **network protocol analyzer** that captures, inspects, and analyzes network packets. It is widely used for:

##### Use Cases

1. **Network Troubleshooting**
    
    - Diagnose latency, packet loss, retransmissions.
        
    - Detect connection resets and handshake failures.
        
2. **Security Monitoring**
    
    - Spot rogue devices, malware communication, ARP spoofing.
        
    - Detect abnormal port/protocol usage (e.g., SSH on port 1234).
        
3. **Protocol Learning & Investigation**
    
    - Dissect layers (Ethernet → IP → TCP/UDP → App).
        
    - Analyze HTTP, DNS, TLS, ICMP, etc.
        

> ⚠️ **Note**: Wireshark is not an Intrusion Detection System (IDS). It **cannot detect** or **block threats**; it only **displays captured packets**. The analyst must investigate manually.

---

#### Starting Wireshark & GUI Overview

When you open Wireshark, the interface contains:

|Section|Description|
|---|---|
|**Toolbar**|Access menus: File, Capture, Analyze, Statistics. Contains capture and stop icons.|
|**Display Filter Bar**|Input area for filters (e.g., `ip.addr == 192.168.1.1`). Filters loaded packets.|
|**Recent Files**|Quick access to previously opened `.pcap` files.|
|**Capture Interfaces**|List of interfaces (e.g., `eth0`, `lo`, `wlan0`). Double-click to start capture.|
|**Status Bar**|Shows profile in use, packet count, and capture status.|

---

#### Capture Live Traffic

##### Steps:

1. Open Wireshark.
    
2. Choose an interface (e.g., `wlan0` for Wi-Fi, `eth0` for LAN).
    
3. Click the **blue shark fin** icon to start capturing.
    
4. Stop capture using the **red square** icon.
    

#### Capture Filters (Set **before** capture)

Applied via the interface selection page.

|Example|Purpose|
|---|---|
|`port 80`|Capture only HTTP packets|
|`host 192.168.0.5`|Capture packets to/from this IP|
|`tcp`|Capture TCP traffic only|

---

#### Loading & Exploring `.pcap` Files

##### Steps:

1. Go to `File → Open`.
    
2. Select your `.pcap` file (from `tcpdump` or other tools).
    
3. The captured packets will be displayed in 3 panes:
    

---

#### Three Main Panes (After Loading or Capturing)

|Pane|Function|
|---|---|
|**Packet List Pane**|Displays one line per packet: No., Time, Source, Destination, Protocol, Info.|
|**Packet Details Pane**|Dissects protocols layer-by-layer (Ethernet, IP, TCP, Application).|
|**Packet Bytes Pane**|Shows raw data in **hex and ASCII**. Highlights parts selected in details pane.|

##### Example:

Click on a TCP packet → expand the **TCP** section → view flags (SYN, ACK), ports, and sequence numbers.

---

#### Display Filters (Post-Capture Filtering)

Write display filters in the **Display Filter Bar**. These are powerful and allow deep inspection.

|Filter|Meaning|
|---|---|
|`ip.addr == 192.168.0.1`|Match packets to/from this IP|
|`tcp.port == 80`|Match HTTP traffic|
|`http.request.method == "GET"`|Show HTTP GET requests|
|`tcp.flags.syn == 1 and tcp.flags.ack == 0`|Filter only SYN packets (start of handshake)|
|`frame contains "admin"`|Show packets containing "admin" keyword|
|`dns.qry.name == "example.com"`|Show DNS queries for domain|

##### Tips:

- Use **autocomplete suggestions** while typing filters.
    
- Right-click any field in the packet details pane → "Apply as Filter" or "Prepare as Filter".
    

---

#### Packet Coloring

Wireshark uses default coloring to highlight traffic types and anomalies.

|Color|Meaning|
|---|---|
|Green|TCP traffic|
|Light blue|UDP traffic|
|Black|TCP retransmissions or malformed packets|
|Dark blue|DNS packets|

##### Modify:

`View → Coloring Rules`

---

#### Expert Info Window

Provides a summary of all protocol-level issues detected.

##### Steps:

- Go to `Analyze → Expert Information`
    

You’ll see tabs like:

- **Errors** (e.g., checksum errors)
    
- **Warnings** (e.g., suspicious retransmits)
    
- **Notes** (e.g., handshake completion)
    

---

#### Follow TCP/HTTP Stream

Reconstructs a full session (chat, request, download).

##### Steps:

1. Right-click on any TCP or HTTP packet
    
2. Select `Follow → TCP Stream` or `Follow → HTTP Stream`
    

Result: Complete conversation in plain text or byte stream format.

---

#### Time Analysis Options

Change time display to analyze latency or retransmissions.

##### Steps:

- `View → Time Display Format`:
    
    - **Date and Time of Day**
        
    - **Seconds Since First Packet**
        
    - **Seconds Since Previous Displayed Packet**
        

---

#### Packet Marking, Comments & Navigation

|Feature|Use|
|---|---|
|`Ctrl + G`|Go to packet number|
|`Ctrl + M`|Mark packet (highlighted in black)|
|`Edit → Packet Comments`|Add notes to selected packet|
|`Edit → Find Packet`|Search by string or filter expression|

---

#### Exporting Packets & Data

##### Export Selected Packets

- Go to `File → Export Specified Packets`
    
- Choose marked/filtered/all packets
    
- Save as `.pcap` or `.pcapng`
    

##### Export HTTP/SMB Files

- Go to `File → Export Objects → HTTP/SMB`
    
- View and extract downloaded/uploaded files
    

---

#### Merge `.pcap` Files

Merge multiple capture files for analysis.

##### Steps:

- `File → Merge`
    
- Select another `.pcap`
    
- Choose **Time Order** to merge chronologically
    

---

#### Conversation Filters

Right-click on IP/Port info → Filter:

- `ip.addr == X.X.X.X`
    
- `tcp.port == 443`
    
- `tcp.stream eq 2` (filter specific session)
    

Also accessible via:

- `Statistics → Conversations`
    

---

#### Common Tasks & Example Filters

|Task|Filter|
|---|---|
|Show all HTTP traffic|`http`|
|Show all DNS queries|`dns`|
|Show ICMP traffic|`icmp`|
|Show packets to/from host|`ip.addr == 10.0.0.5`|
|Show TCP handshake|`tcp.flags.syn == 1 and tcp.flags.ack == 0`|
|Extract keyword|`frame contains "password"`|

---

#### Final Tips

- Run Wireshark as root/admin to access all interfaces.
    
- Use **tcpdump** to capture in CLI and analyze later in Wireshark:
    
    ```bash
    sudo tcpdump -i eth0 -w capture.pcap
    ```
    
- Then open `capture.pcap` in Wireshark for GUI analysis.
    

---