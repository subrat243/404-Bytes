# Linux CLI

A practical guide to the Linux command line for penetration testers and security professionals. Covers essential commands, file system navigation, process management, user permissions, and basic system administration tasks. Emphasis is placed on efficient command usage for reconnaissance and enumeration.

**Purpose**: To equip learners with the necessary command-line skills to navigate, understand, and interact with Linux systems effectively during penetration tests.

---

## Key Concepts

- File System Navigation
- Process Management
- User Permissions
- Basic System Administration
- Command Efficiency
- Reconnaissance
- Enumeration

---

## Examples

```bash
# File System Navigation
cd /var/www/html
ls -la
find / -name "config.php"

# Process Management
ps aux | grep apache
kill -9 1234

# User Permissions
chmod 777 script.sh
chown root:root /etc/shadow

# Reconnaissance
cat /etc/passwd
cat /etc/hosts
cat /etc/issue
```
