## 1. Introduction to Forensics and Digital Forensics

- **Forensics**: Application of methods and procedures to investigate and solve crimes.
    
- **Digital Forensics**: Branch of forensics that investigates cyber crimes by examining digital devices.
    
- **Cyber Crime**: Any criminal activity conducted using digital devices. Examples: hacking, data theft, online fraud, ransomware.
    

### Example Case

- A bank robber’s home is raided. Devices such as a laptop, mobile phone, hard drive, and USB are seized.
    
- Evidence found:
    
    - Bank map and escape routes on the laptop and hard drive.
        
    - Security control document showing bypass plans.
        
    - Media files of previous robberies.
        
    - Chat groups and call records related to the robbery on the mobile phone.
        
- This evidence is analyzed and presented in court to support legal proceedings.
    

---

## 2. Digital Forensics Process (NIST Four Phases)

### (i) Collection

- Identify all potential evidence sources: computers, laptops, USBs, cameras, etc.
    
- Ensure data integrity (no tampering).
    
- Maintain documentation of collected evidence.
    

### (ii) Examination

- Large datasets are filtered to extract relevant evidence.
    
- Example: From a camera, only select photos/videos taken at the crime date/time.
    
- Reduces irrelevant data and prepares it for analysis.
    

### (iii) Analysis

- Correlate multiple pieces of evidence to build a timeline of events.
    
- Example: Matching chat records with security documents to prove intent.
    
- Aim: Derive activities relevant to the case.
    

### (iv) Reporting

- Prepare a detailed report of methodology, findings, and recommendations.
    
- Include both technical findings and executive summaries for law enforcement and management.
    

---

## 3. Types of Digital Forensics

1. **Computer Forensics**:
    
    - Investigates computers (common devices in crimes).
        
    - Example: Extracting deleted files from a suspect’s PC.
        
2. **Mobile Forensics**:
    
    - Focuses on mobile phones/tablets.
        
    - Extracts call logs, texts, GPS locations, browsing history.
        
    - Example: Linking GPS data to a suspect’s location during a robbery.
        
3. **Network Forensics**:
    
    - Investigates network traffic and logs.
        
    - Example: Detecting an attacker’s IP in a corporate breach.
        
4. **Database Forensics**:
    
    - Examines databases for unauthorized access, modifications, or data theft.
        
    - Example: Identifying altered customer records in a financial database.
        
5. **Cloud Forensics**:
    
    - Investigates data hosted on cloud platforms.
        
    - Challenges include distributed storage and limited physical evidence.
        
    - Example: Tracking stolen files uploaded to Google Drive.
        
6. **Email Forensics**:
    
    - Analyzes emails for phishing, scams, and fraud.
        
    - Example: Identifying a forged email header in a spear-phishing attack.
        

---

## 4. Evidence Acquisition Practices

### a) Proper Authorization

- Evidence must be collected with legal authorization.
    
- Unauthorized evidence is inadmissible in court.
    

### b) Chain of Custody

- A formal document tracking evidence handling.
    
- Includes:
    
    - Description of evidence.
        
    - Collector’s name.
        
    - Date/time of collection.
        
    - Storage location.
        
    - Access logs.
        
- Ensures accountability and reliability.
    

### c) Use of Write Blockers

- Prevents alteration of data during acquisition.
    
- Example: If a suspect’s hard drive is connected without a write blocker, file timestamps may change. Write blockers avoid this issue.
    

---

## 5. Forensic Imaging

### a) Disk Image

- Bit-by-bit copy of storage (HDD/SSD).
    
- Contains non-volatile data (files, browsing history, documents).
    
- Example: Recovery of deleted emails from a disk image.
    

### b) Memory Image

- Copy of system RAM (volatile data).
    
- Must be taken before shutdown/restart.
    
- Contains open files, running processes, network connections.
    
- Example: Identifying a malware process running in memory.
    

---

## 6. Tools for Acquisition and Analysis

1. **FTK Imager**
    
    - Disk imaging tool with GUI.
        
    - Supports analysis of image contents.
        
    - Example: Previewing files and creating forensic disk images.
        
2. **Autopsy**
    
    - Open-source forensic analysis tool.
        
    - Features: keyword search, deleted file recovery, metadata analysis.
        
    - Example: Detecting file extension mismatch (.jpg renamed as .exe).
        
3. **DumpIt**
    
    - CLI tool for memory acquisition on Windows.
        
    - Creates memory dumps for analysis.
        
4. **Volatility**
    
    - Open-source memory forensics framework.
        
    - Plugins for analyzing processes, registry, and network connections.
        
    - Example: Using `pslist` to view running processes at time of acquisition.
        

---

## 7. Metadata in Digital Evidence

### a) PDF Metadata

- Tool: `pdfinfo` (from `poppler-utils`).
    
- Extracts author, creation date, software used.
    
- Example: A PDF created with MS Word on Oct 10, 2018, could link the suspect to document creation.
    

### b) Photo EXIF Data

- Metadata embedded in images.
    
- Contains camera model, date/time, and GPS coordinates.
    
- Tool: `exiftool` (Kali: `sudo apt install libimage-exiftool-perl`).
    
- Example: GPS data `51°30'51.9"N 0°05'38.7"W` leads investigators to the street where a crime photo was taken.
    

---
# Digital Forensics (THM)

- **Digital Forensics** investigates cyber crimes using structured procedures.
    
- **NIST Process**: Collection → Examination → Analysis → Reporting.
    
- **Evidence Handling** requires authorization, chain of custody, and write blockers.
    
- **Forensic Imaging** captures both disk and volatile memory data.
    
- **Tools**: FTK Imager, Autopsy, DumpIt, Volatility.
    
- **Metadata Analysis**: PDF info and EXIF data provide critical leads.

---
