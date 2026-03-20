# John the Ripper

## **1. Basic Syntax**

**Command Structure:**

```bash
john [options] [file path]
```

- **`john`** → Runs John the Ripper
    
- **`[options]`** → Flags that control cracking mode, wordlists, formats, etc.
    
- **`[file path]`** → File containing the hash you want to crack (no path needed if in same directory).
    

**Example:**

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

---

## **2. Automatic Cracking**

Uses a given **wordlist** to try passwords against the hash file.

**Syntax:**

```bash
john --wordlist=[path_to_wordlist] [path_to_file]
```

**Example:**

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

---

## **3. Identifying Hashes**

Sometimes John cannot detect hash type automatically.

**Tool:** `hash-identifier` (Python-based).

**Usage:**

```bash
python3 hash-id.py
```

Enter hash → tool suggests possible hash types.

**Example Output:**

```
HASH: 2e728dd31fb5949bc39cac5a9f066498
Possible Hashes:
[+] MD5
[+] Domain Cached Credentials
```

---

## **4. Format-Specific Cracking**

If you know the hash type, specify it using `--format`.

**Syntax:**

```bash
john --format=[format] --wordlist=[wordlist] [file]
```

**Example:**

```bash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

**Tip:**  
List all formats:

```bash
john --list=formats | grep -i md5
```

---

## **5. NTHash / NTLM Cracking**

- Used by **Windows** in `SAM` database or `NTDS.dit`.
    
- Can be cracked or used in **Pass-the-Hash** attacks.
    

**Syntax:**

```bash
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt
```

---

## **6. Cracking `/etc/shadow` Passwords**

`/etc/shadow` → Stores hashed passwords (root-only access).  
Requires combining with `/etc/passwd` using `unshadow`.

### **Unshadow Command**

```bash
unshadow [passwd_file] [shadow_file] > unshadowed.txt
```

**Example:**

```bash
unshadow local_passwd local_shadow > unshadowed.txt
```

---

### **Cracking the Unshadowed File**

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt
```

---

## **7. Single Crack Mode & Word Mangling**

- Uses **username** and GECOS field info to generate guesses.
    
- Mangling = changing words slightly (adding numbers, symbols, case change).
    

**Syntax:**

```bash
john --single --format=[format] hashes.txt
```

**File Format:**  
Instead of:

```
1efee03cdcb96d90ad48ccc7b8666033
```

Write:

```
mike:1efee03cdcb96d90ad48ccc7b8666033
```

**Example:**

```bash
john --single --format=raw-sha256 hashes.txt
```

---

## **8. Custom Rules**

Rules define password patterns John should try.

**Location:** `/etc/john/john.conf` or `/opt/john/john.conf`

**Example Rule:** For passwords like `Polopassword1!`

```conf
[List.Rules:PoloPassword]
cAz"[0-9][!£$%@]"
```

- `c` → Capitalise first letter
    
- `Az` → Append characters
    
- `[0-9]` → Any number
    
- `[!£$%@]` → Any listed symbol
    

**Run with Rule:**

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --rule=PoloPassword hash.txt
```

---

## **9. Cracking Archive Files**

### **ZIP Files – zip2john**

Convert ZIP to John-readable hash:

```bash
zip2john file.zip > zip_hash.txt
```

Crack:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
```

### **RAR Files – rar2john**

Convert RAR to hash:

```bash
rar2john file.rar > rar_hash.txt
```

Crack:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt
```

---

## **10. Cracking SSH Private Key Passwords**

### **ssh2john**

Convert `id_rsa` private key to hash:

```bash
/opt/john/ssh2john.py id_rsa > id_rsa_hash.txt
```

Crack:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt
```

---

## **11. Summary Table of Commands**

|**Purpose**|**Conversion Tool**|**Example Command**|
|---|---|---|
|Crack basic hash|–|`john --wordlist=rockyou.txt hash.txt`|
|Format-specific cracking|–|`john --format=raw-md5 --wordlist=rockyou.txt hash.txt`|
|Unshadow Linux hashes|`unshadow`|`unshadow passwd shadow > file.txt`|
|Crack ZIP file|`zip2john`|`zip2john file.zip > hash.txt`|
|Crack RAR file|`rar2john`|`rar2john file.rar > hash.txt`|
|Crack SSH private key|`ssh2john`|`ssh2john id_rsa > hash.txt`|
|Single crack (word mangling)|–|`john --single --format=raw-sha256 file.txt`|
|Use custom rules|–|`john --wordlist=rockyou.txt --rule=RuleName file.txt`|

---