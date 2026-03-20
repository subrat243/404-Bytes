## 1. Introduction

- Technology has improved efficiency, but it also introduces risks.
    
- Organizations store confidential data ("secrets") digitally in systems and networks.
    
- Threat actors exploit vulnerabilities daily, making cybersecurity critical.
    
- **Traditional security is insufficient**, hence the need for a **SOC**.
    

### SOC Definition

- **SOC (Security Operations Center)**: A dedicated facility with a specialized team monitoring, detecting, and responding to threats 24/7.
    
- **Primary Goal**: Continuous monitoring, detection, and response to security incidents.
    

---

## 2. Core Functions of SOC

### Detection

SOC detects harmful activities using security solutions and monitoring techniques:

1. **Detect Vulnerabilities**
    
    - Weaknesses in systems that attackers may exploit.
        
    - Example: SOC identifies Windows computers missing critical patches.
        
2. **Detect Unauthorized Activity**
    
    - Detects login attempts or activity using stolen credentials.
        
    - Example: User logs in from two distant countries within minutes → suspicious.
        
3. **Detect Policy Violations**
    
    - Ensures compliance with security policies.
        
    - Example: Employee downloading pirated movies or sending company data via personal email.
        
4. **Detect Intrusions**
    
    - Unauthorized access to systems/networks.
        
    - Example: Attacker exploiting a vulnerable web app, or user infected by a malicious website.
        

---

### Response

- After detection, SOC responds to minimize impact and investigate the root cause.
    
- Works with the **Incident Response (IR) team** to contain, eradicate, and recover.
    

---

## 3. The Three Pillars of SOC

1. **People**
    
    - Trained professionals monitoring and responding to alerts.
        
    - Human judgment is necessary to filter real threats from false alarms.
        
    - Example: Just like firefighters ignore false alarms caused by smoke from cooking, SOC analysts ignore false positives.
        
2. **Process**
    
    - Defined procedures and workflows for incident handling.
        
    - Includes alert triage, reporting, escalation, and incident response.
        
3. **Technology**
    
    - Security solutions to centralize monitoring and automate detection.
        
    - Examples: SIEM, EDR, Firewalls, IDS/IPS.
        

---

## 4. SOC Roles and Responsibilities

1. **SOC Analyst (Level 1)**
    
    - First line of defense.
        
    - Performs **alert triage** (deciding whether an alert is harmful).
        
    - Example: L1 checks if malware detection is a false alarm or real.
        
2. **SOC Analyst (Level 2)**
    
    - Performs deeper investigations.
        
    - Correlates data across multiple sources.
        
    - Example: Investigating logs from SIEM and endpoint activity together.
        
3. **SOC Analyst (Level 3)**
    
    - Experienced professionals.
        
    - Handles critical incidents (containment, eradication, recovery).
        
    - Example: An advanced malware outbreak requiring forensic investigation.
        
4. **Security Engineer**
    
    - Deploys and configures security tools (SIEM, EDR, firewalls).
        
    - Ensures smooth operation of SOC infrastructure.
        
5. **Detection Engineer**
    
    - Develops and fine-tunes detection rules.
        
    - Example: Creating SIEM rules to detect brute force logins.
        
6. **SOC Manager**
    
    - Oversees SOC processes and team performance.
        
    - Reports SOC posture to the **CISO (Chief Information Security Officer)**.
        

---

## 5. SOC Processes

### a) Alert Triage

- First step after an alert is generated.
    
- Analysts answer **5 Ws** to evaluate severity and priority.
    

**Example Alert:** Malware detected on Host: GEORGE PC

| 5 Ws   | Answer                                        |
| ------ | --------------------------------------------- |
| What?  | Malicious file detected on host.              |
| When?  | 13:20, June 5, 2024.                          |
| Where? | Directory on host "GEORGE PC".                |
| Who?   | User George.                                  |
| Why?   | File downloaded from a pirated software site. |

---

### b) Reporting

- Escalate harmful alerts to higher-level analysts.
    
- Reports should include:
    
    - All 5 Ws.
        
    - Analysis details.
        
    - Evidence (screenshots/logs).
        

---

### c) Incident Response and Forensics

- Critical alerts trigger **incident response**.
    
- Includes containment, eradication, recovery, and forensic analysis.
    
- Example: An intrusion detected on a server → forensic team analyzes logs and memory dumps to find root cause.
    

---

## 6. Technology in SOC

### a) SIEM (Security Information and Event Management)

- Collects and correlates logs from different sources.
    
- Uses detection rules, user behavior analytics, and threat intelligence.
    
- Example: SIEM detects multiple failed logins followed by a successful one → possible brute force.
    
- Limitation: SIEM provides **detection only**, not response.
    

### b) EDR (Endpoint Detection and Response)

- Provides real-time monitoring of endpoints (PCs, laptops, servers).
    
- Can automate responses like isolating a machine.
    
- Example: Detecting ransomware behavior and immediately stopping the process.
    

### c) Firewall

- Network barrier between internal and external traffic.
    
- Filters unauthorized access.
    
- Example: Blocking inbound traffic from blacklisted IP addresses.
    

### d) Other Security Tools

- **Antivirus/EPP**: Protects endpoints against malware.
    
- **IDS/IPS**: Intrusion Detection/Prevention Systems.
    
- **XDR (Extended Detection and Response)**: Integrates multiple security layers (endpoint, network, cloud).
    
- **SOAR (Security Orchestration, Automation, and Response)**: Automates SOC workflows and incident handling.
    

---

# Summary

- A **SOC** provides centralized monitoring and incident response.
    
- Core functions: **Detection** (vulnerabilities, unauthorized activity, policy violations, intrusions) and **Response** (minimizing impact, root cause analysis).
    
- Three pillars: **People, Process, Technology**.
    
- SOC team roles: Analysts (L1–L3), Security Engineer, Detection Engineer, SOC Manager.
    
- Processes: **Alert Triage (5 Ws), Reporting, Incident Response, Forensics**.
    
- Technologies: **SIEM, EDR, Firewalls, IDS/IPS, SOAR, XDR**.
    

---
