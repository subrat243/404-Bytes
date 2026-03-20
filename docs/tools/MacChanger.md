### Introduction

Every device has a unique **MAC address** assigned by the manufacturer. When connecting to a router, this MAC address can be logged and potentially reveal your device identity.

MacChanger helps in spoofing or changing the MAC address temporarily.

---

### Installation

```bash
sudo apt install macchanger
```

---

### Usage

1. List all interfaces:
    
    ```bash
    ip link show
    ```
    
2. Change MAC address randomly:
    
    ```bash
    sudo macchanger -r ens33
    ```
    
    Replace `ens33` with your interface name (for example, `eth0`, `wlan0`).
    
3. Set a specific MAC address:
    
    ```bash
    sudo macchanger -m 00:11:22:33:44:55 ens33
    ```
    
4. Restore the original MAC address:
    
    ```bash
    sudo macchanger -p ens33
    ```
    

---

### Notes

- The change is temporary and will revert back after a system reboot.
    
- Useful for avoiding tracking on WiFi networks and during ethical hacking operations.
    
- Some networks may not allow spoofed MACs, so usage may vary.
    

---
