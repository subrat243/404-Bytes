## Introduction

Proxychains is a tool that allows you to redirect network traffic of an application through one or more proxy servers. Instead of using a single proxy, it can use multiple proxies in a chain, making it much harder to trace the real IP address.

This is particularly useful in ethical hacking and penetration testing where anonymity is required.

---

### Installation

Install Proxychains with the following command:

```bash
sudo apt-get install proxychains
```

---
## Key Configuration Parameters

1. **dynamic_chain**
    
    - When enabled, Proxychains will dynamically choose proxies from the list.
        
    - If one proxy is offline, it will automatically skip to the next.
        
    - Recommended for practical usage because free proxies often go down.
        
    
    Example:
    
    `dynamic_chain`
    
2. **strict_chain**
    
    - Proxychains will use proxies in the exact order listed.
        
    - If one proxy fails, the entire chain will fail.
        
    - Useful when you need a strict sequence of proxies.
        
    
    Example (usually commented out if using dynamic_chain):
    
    `# strict_chain`
    
3. **proxy_dns**
    
    - Ensures that DNS queries are also routed through the proxy servers instead of leaking from your local ISP.
        
    - Without this, your domain lookups may reveal your original IP even if traffic is proxied.
        
    
    Example:
    
    `proxy_dns`
    
4. **tcp_read_time_out** and **tcp_connect_time_out**
    
    - These define how long Proxychains should wait before timing out.
        
    - Useful to prevent tools from hanging when proxies are slow or unresponsive.
        
    
    Example:
    
    `tcp_read_time_out 15000 tcp_connect_time_out 8000`
    
5. **[ProxyList] Section**
    
    - This is where you specify the proxies you want to use.
        
    - Format:
        
        `<proxy_type> <ip_address> <port>`
        
    - Supported proxy types: `socks4`, `socks5`, `http`.
        
    
    Example:
    
    `socks5 127.0.0.1 9050 http 192.168.1.1 8080 socks4 36.66.210.159 44034`

---
### Configuration

The configuration file is located at:  
`/etc/proxychains.conf` (or `/etc/proxychains4.conf` in some systems).

1. Open the configuration file in a text editor:
    
    ```bash
    sudo nano /etc/proxychains.conf
    ```
    
2. Change the chain type:
    
    - Find the section with `strict_chain` and `dynamic_chain`.
        
    - By default, `strict_chain` is enabled, and `dynamic_chain` is commented.
        
    - Comment out `strict_chain` and uncomment `dynamic_chain`.
        
        - `strict_chain`: Proxychains will stop working if one proxy in the chain is down.
            
        - `dynamic_chain`: Proxychains will skip dead proxies and continue with the available ones.
            
    
    Example:
    
    ```
    # strict_chain
    dynamic_chain
    ```
    
3. Enable DNS proxying:  
    Find the line with `proxy_dns` and uncomment it. This ensures DNS queries also go through proxies.
    
4. Add proxy servers under the `[ProxyList]` section at the bottom of the file. Proxies are added in the format:
    
    ```
    <proxy_type> <IP_address> <Port>
    ```
    
    Example:
    
    ```
    socks4 36.66.210.159 44034
    socks4 182.52.51.19 32591
    socks4 167.249.78.22 4145
    socks4 91.195.158.171 4145
    socks4 45.4.255.168 4145
    socks4 31.145.166.28 4145
    ```
    
    You can find free proxy lists by searching “proxy server list” on the internet.
    
5. Save and exit the file:
    
    - Press `Ctrl+O` to save.
        
    - Press `Ctrl+X` to exit.
        

---

### Usage

Once configured, run any application through Proxychains by prefixing it with `proxychains`.

Examples:

- To open a browser anonymously:
    
    ```bash
    proxychains firefox www.dnsleaktest.com
    ```
    
- To run Nmap with proxies:
    
    ```bash
    proxychains nmap -sT target.com
    ```
    
- To run SQLmap:
    
    ```bash
    proxychains sqlmap -u "http://target.com/page?id=1"
    ```
    
---
