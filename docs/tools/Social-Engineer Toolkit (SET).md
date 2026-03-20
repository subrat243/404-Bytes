# Social-Engineer Toolkit (SET)

The Social-Engineer Toolkit (SET) is a Python-based framework designed for simulating social-engineering attacks during penetration testing. It includes modules for phishing, credential harvesting, payload generation, and training simulations.

Use only in legal and controlled environments with explicit permission.

# Requirements

- Kali Linux (Recommended) or any OS with Python 3
    
- Administrator/root privileges
    
- Internal lab network or test VM environment
    

# Launching SET

To start SET:

```bash
sudo setoolkit
```

Main Menu:

```
1) Social-Engineering Attacks
2) Penetration Testing (Fast-Track) Attacks
3) Third Party Modules
4) Update the Social-Engineer Toolkit
5) Update SET configuration
6) Exit the Social-Engineer Toolkit
```

Most operations are performed inside **Social-Engineering Attacks**.

# Key Attack Modules

|Menu Option|Technique|Purpose|
|---|---|---|
|Website Attack Vectors|Credential harvesting, cloning sites|Fake login pages, capture passwords|
|Spear-Phishing Attack Vectors|Email campaigns with payloads|Phishing simulations|
|Infectious Media Generator|Malicious USB/training payloads|Awareness training|
|Create Payload and Listener|Reverse shells|Red team labs|
|Mass Mailer Attack|Email phishing simulations|Awareness testing|

# Credential Harvester Attack (Site Cloner) – Full Example

Scenario: Demonstrate phishing in a lab.  
Attacker IP: `192.168.56.10`  
Training web login page: `http://192.168.56.20/login`

### Steps:

1. Start the tool
    

```bash
sudo setoolkit
```

2. Select Social-Engineering Attacks
    

```
1
```

3. Select Website Attack Vectors
    

```
2
```

4. Select Credential Harvester Attack Method
    

```
3
```

5. Choose Site Cloner
    

```
2
```

6. Enter your attacker machine IP
    

```
192.168.56.10
```

Purpose: Submissions will be captured here.

7. Enter the URL to clone
    

```
http://192.168.56.20/login
```

SET automatically:

- Downloads/clones the page
    
- Rewrites the form POST actions
    
- Hosts the cloned page on port 80
    

8. Wait for logs to appear when a test user submits credentials:
    

Example output:

```
[*] WE GOT A HIT!
username: testuser
password: Password123
```

9. Logs are saved under SET logs directory.
    

# Mass Mailer Attack – Quick Usage

1. Launch SET
    

```bash
sudo setoolkit
```

2. Menu → Social-Engineering Attacks → Mass Mailer Attack
    

```
1 → 5
```

3. Select delivery mode:
    

- Single target
    
- Email list
    
- File containing multiple emails
    

4. Enter SMTP settings
    

Examples for a local training mail server:

```
SMTP Address: 192.168.56.30
Port: 25
Email: security-team@lab.local
```

5. Provide subject and body  
    Include your lab phishing URL:
    

```
http://192.168.56.10
```

Victim users receive training emails and when they click, credentials are captured by the previous module.

# Logs and Reports

Typical Paths:

```
/var/lib/setoolkit/logs/
or
/root/.set/reports/
```

Logs contain:

- Collected credentials
    
- POST data
    
- Timestamps
    
- Visited IP addresses
    

Access must be restricted to authorized personnel.

# Common Options and Troubleshooting

|Issue|Fix|
|---|---|
|Port 80 already in use|Change in `/etc/setoolkit/set.config` → `WEB_PORT = 8080`|
|Cloned page loads without styling|The original website’s assets are external; use custom import|
|Email not delivered|Use a correct SMTP server or enable sendmail|
|Redirection fails after login|Enter redirect URL in configuration|

# Defensive Learnings

SET simulations help organizations improve:

- Email security filtering
    
- URL verification behavior
    
- MFA enforcement
    
- HTTPS adoption (prevents credential interception)
    
- Employee cybersecurity training
    

# Summary

|Feature|Description|
|---|---|
|Site Cloner|Host fake lab login pages for training|
|Credential Harvester|Capture login form submissions|
|Mass Mailer|Send phishing simulations to test awareness|
|Payload Generation|Create controlled shells during penetration tests|

SET is one of the most widely used tools in professional social-engineering test environments.

---
