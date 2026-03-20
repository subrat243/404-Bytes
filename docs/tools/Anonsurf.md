# Anonsurf

### Introduction

Anonsurf is a tool that routes **all system traffic** through the Tor network. Unlike Proxychains (which works application by application), Anonsurf anonymizes the entire operating system.

---

### Installation

1. Clone the Anonsurf repository:
    
    ```bash
    git clone https://github.com/Und3rf10w/kali-anonsurf.git
    ```
    
2. Move into the directory:
    
    ```bash
    cd kali-anonsurf/
    ```
    
3. Run the installer:
    
    ```bash
    ./installer.sh
    ```
    

Alternatively, on some systems, you can install directly:

```bash
sudo apt-get update
sudo apt-get install anonsurf
```

---

### Usage

- Start Anonsurf:
    
    ```bash
    sudo anonsurf start
    ```
    
- Stop Anonsurf:
    
    ```bash
    sudo anonsurf stop
    ```
    
- Restart Anonsurf:
    
    ```bash
    sudo anonsurf restart
    ```
    
- Change identity (request a new Tor circuit):
    
    ```bash
    sudo anonsurf change
    ```
    
- Check Anonsurf status:
    
    ```bash
    sudo anonsurf status
    ```
    

---

### Features

- Routes all traffic through the Tor network.
    
- Prevents DNS leaks.
    
- Blocks IPv6 traffic to avoid accidental identity leaks.
    
- Provides a system-wide anonymity solution.
    

---

### Verification

Before starting Anonsurf:

- Visit [https://www.dnsleaktest.com](https://www.dnsleaktest.com/) to see your real IP.
    

After starting Anonsurf:

- Visit the same site and confirm your IP has changed to a Tor exit node.
    