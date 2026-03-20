`netdiscover` is a network scanning tool used for discovering live hosts on a network. It operates primarily by sending ARP (Address Resolution Protocol) requests, making it a fast and effective scanner for local networks. It can operate in both active and passive modes.

---

## How It Works

At its core, `netdiscover` works on **Layer 2** of the OSI model.

- **Active Mode:** It actively sends ARP request packets to all possible IP addresses within a specified or auto-detected network range. Hosts that are online will respond with an ARP reply, revealing their IP address and MAC address.
    
- **Passive Mode:** Instead of sending out packets, it silently listens (sniffs) for ARP traffic on the network. This allows it to build a map of active hosts without generating any network noise, making it much stealthier.
    

---

## Common Options and Usage

The basic syntax for the tool is:

|Option|Description|
|---|---|
|`-i <device>`|Specifies the network interface to scan/sniff on (e.g., `eth0`, `wlan0`).|
|`-r <range>`|Scans a specific IP range instead of auto-detecting. Use CIDR notation (e.g., `192.168.1.0/24`).|
|`-p`|Enables passive mode. It will only listen for ARP traffic and will not send any packets.|
|`-l <file>`|Scans a list of ranges contained in the given file.|
|`-f`|Enables fast mode scan, which sends fewer packets and waits less time for responses.|
|`-c <count>`|Sets the number of times to send each ARP request. Default is 1.|

---

## Practical Examples

### 1. Basic Active Scan

To perform a standard active scan on a specific network interface, you first need to identify your network interface name using a command like `ip a` or `ifconfig`. Once you have it, run the following:

Bash

```bash
# Replace eth0 with your network interface
sudo netdiscover -i eth0
```

The tool will automatically detect the network range and start sending ARP requests.

### 2. Scanning a Specific IP Range

If you want to limit the scan to a specific subnet, use the `-r` flag. This is the most common use case.

Bash

```bash
# Scan the 192.168.1.0/24 network on the eth0 interface
sudo netdiscover -i eth0 -r 192.168.1.0/24
```

### 3. Passive Reconnaissance

To discover hosts without actively probing the network, use passive mode. This is useful for stealthy information gathering. The tool will simply listen for any ARP packets and log the hosts it sees.

Bash

```bash
# Listen silently on the wlan0 interface
sudo netdiscover -p -i wlan0
```

---

## Understanding the Output

When running, `netdiscover` displays a real-time list of discovered hosts with the following columns:

- **IP:** The IP address of the discovered host.
    
- **MAC Address:** The hardware address of the host's network interface card.
    
- **Count:** The number of ARP packets received from this host.
    
- **Len:** The total size of packets received from the host.
    
- **MAC Vendor:** The manufacturer of the network card, identified from the MAC address.
    
- **Hostname:** The DNS hostname, if it can be resolved.