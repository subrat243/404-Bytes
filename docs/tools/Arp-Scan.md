`arp-scan` is a command-line tool used to discover hosts on a local network by sending and receiving ARP (Address Resolution Protocol) packets. It provides a quick and effective way to identify active devices and map their IP addresses to MAC addresses.

---

## How It Works

The tool operates at Layer 2 (Data Link Layer) of the OSI model. It constructs and transmits ARP request packets for every IP address in a specified range on the local network. Active hosts that receive these requests will reply with an ARP response packet containing their MAC address. `arp-scan` listens for these responses, decodes them, and displays a table of IP addresses, MAC addresses, and the corresponding hardware vendor.

---

## Installation

You can install `arp-scan` using the default package manager for your Linux distribution.

- **For Debian/Ubuntu-based systems:**
    
    ```bash
    sudo apt-get update
    sudo apt-get install arp-scan
    ```
    
- **For RHEL/Fedora/CentOS-based systems:**
    
    ```bash
    sudo dnf install arp-scan
    ```
    
- **For Arch Linux-based systems:**
    
    ```bash
    sudo pacman -S arp-scan
    ```
    

---

## Usage and Examples

**Note:** `arp-scan` requires root privileges to create raw sockets for sending and receiving ARP packets.

### Basic Scan

To scan all hosts on your local network, `arp-scan` automatically determines the network configuration from the primary network interface.

- **Command:**
    
    ```bash
    sudo arp-scan --localnet
    ```
    
    or the shorter version:
    
    ```bash
    sudo arp-scan -l
    ```
    
- **Example Output:**
    
    ```bash
    Interface: eth0, type: EN10MB, MAC: 00:11:22:33:44:55, IPv4: 192.168.1.10
    Starting arp-scan 1.9.7 with 256 hosts (https://github.com/royhills/arp-scan)
    192.168.1.1     a0:b1:c2:d3:e4:f5   (Unknown)
    192.168.1.5     00:aa:bb:cc:dd:ee   Apple, Inc.
    192.168.1.12    11:22:33:44:55:66   TP-LINK TECHNOLOGIES CO.,LTD.
    ...
    3 packets received by filter, 0 packets dropped by kernel
    Ending arp-scan 1.9.7: 256 hosts scanned in 1.884 seconds (135.88 hosts/sec). 3 responded
    ```
    
    The output displays the **IP Address**, **MAC Address**, and the **Vendor** associated with that MAC address.
    

### Advanced Scans

- **Scan a Specific Network Interface**
    
    If you have multiple network interfaces (e.g., `eth0`, `wlan0`), you can specify which one to use with the `-I` or `--interface` flag.
    
    ```bash
    sudo arp-scan -I wlan0 -l
    ```
    
- **Scan a Custom IP Range**
    
    You can define a specific network range to scan instead of relying on the default. This can be specified using CIDR notation or a start-end range.
    
    ```bash
    # Using CIDR notation
    sudo arp-scan 192.168.1.0/24
    
    # Using a range
    sudo arp-scan 192.168.1.1-192.168.1.100
    ```
    
- **Read Target IPs from a File**
    
    For scanning a non-sequential list of IP addresses, you can list them in a file and pass it to `arp-scan` using the `--file` option.
    
    First, create a file named `targets.txt`:
    
    ```bash
    192.168.1.25
    192.168.1.50
    10.10.0.5
    ```
    
    Then, run the scan:
    
    ```bash
    sudo arp-scan --file=targets.txt
    ```
    
- **Verbose Output**
    
    To get more detailed information about the scanning process, including packets sent and received, use the `-v` or `--verbose` flag.
    
    ```bash
    sudo arp-scan -l -v
    ```

---
