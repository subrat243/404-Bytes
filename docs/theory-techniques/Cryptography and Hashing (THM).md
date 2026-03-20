### **1. Basics of Cryptography**

**Cryptography** is the practice and study of techniques for securing communication and data in the presence of adversaries. It transforms readable data (**plaintext**) into an unreadable format (**ciphertext**) to protect information from unauthorized access.

#### Key Terminology:

- **Plaintext**: The original, readable message or data.
    
- **Ciphertext**: The encrypted, unreadable version of the plaintext.
    
- **Encryption**: The process of converting plaintext into ciphertext.
    
- **Decryption**: The process of converting ciphertext back to plaintext.
    
- **Key**: A string of bits used by cryptographic algorithms to transform data.
    

---

### **2. Importance of Cryptography**

- **Confidentiality**: Ensures that only authorized users can access the data.
    
- **Integrity**: Protects data from being altered without detection.
    
- **Authentication**: Confirms the identity of users or systems.
    
- **Non-repudiation**: Prevents denial of sending a message by the sender.
    

**Example**:

- When you log into your bank account, cryptography ensures your password is securely transmitted and stored in an unreadable format, preventing it from being stolen or modified.
    

---

### **3. Plaintext to Ciphertext Process**

The conversion from plaintext to ciphertext involves an **encryption algorithm** and a **key**.

#### Example:

Let’s consider the plaintext:  
`HELLO`

Using a basic **Caesar Cipher** with a shift of 3:

- H → K
    
- E → H
    
- L → O
    
- L → O
    
- O → R
    

So the ciphertext becomes:  
`KHOOR`

This is a very basic form of encryption for illustrative purposes.

---

### **4. Types of Encryption**

Encryption is mainly classified into two types:

#### **A. Symmetric Encryption**

- **Definition**: Uses the same key for both encryption and decryption.
    
- **Speed**: Faster and efficient for large amounts of data.
    
- **Key Distribution Problem**: Securely sharing the key with the recipient is challenging.
    

**Example**:

- **AES (Advanced Encryption Standard)**
    

Let’s say:

- Plaintext: `DATA`
    
- Key: `123456`
    
- Ciphertext: `X7B$9D` (output depends on the algorithm)
    

Both sender and receiver must use the same key `123456`.

**Use Cases**:

- Encrypting files on a hard drive.
    
- Securing communication between trusted parties.
    

---

#### **B. Asymmetric Encryption**

- **Definition**: Uses two keys – a **public key** for encryption and a **private key** for decryption.
    
- **Security**: More secure for key exchange and communication with untrusted parties.
    
- **Slower**: Computationally intensive, hence used mainly for small data or key exchange.
    

**Example**:

- Algorithm: **RSA (Rivest–Shamir–Adleman)**
    

Steps:

1. Sender encrypts message using the receiver’s **public key**.
    
2. Receiver decrypts it using their **private key**.
    

If:

- Plaintext: `HELLO`
    
- Public Key: Receiver’s public key
    
- Encrypted Message (Ciphertext): `7d3@#2`
    
- Private Key: Receiver uses their private key to decrypt and get `HELLO`.
    

**Use Cases**:

- Secure emails (e.g., PGP)
    
- Digital signatures
    
- SSL/TLS for web security
    

---
### **SSH Key Generation and Public-Key Cryptography**

### **What is `ssh-keygen`?**

`ssh-keygen` is a command-line tool used to generate a **public-private key pair** for use with **SSH (Secure Shell)**. It provides secure authentication without using passwords.

---

## **Types of SSH Keys**

### 1. **DSA (Digital Signature Algorithm)**

- Designed specifically for digital signatures.
    
- Uses discrete logarithm-based security.
    
- Considered outdated and **not recommended** anymore.
    

### 2. **ECDSA (Elliptic Curve Digital Signature Algorithm)**

- A modern alternative to DSA.
    
- Uses elliptic curve cryptography for better security with smaller key sizes.
    
- **Faster** and **more efficient** than RSA and DSA.
    

### 3. **ECDSA-SK (ECDSA with Security Key)**

- A hardware-backed version of ECDSA.
    
- Requires a **security key** like YubiKey or a hardware token.
    
- Enhances private key protection by storing the key on a secure device.
    

### 4. **Ed25519**

- Based on the **EdDSA** algorithm with **Curve25519**.
    
- Modern, fast, and highly secure.
    
- **Recommended** for most users due to strong security and short key size.
    

### 5. **Ed25519-SK**

- Like Ed25519, but the private key resides on a hardware security key.
    
- Offers **hardware-level protection** against key theft.
    

---

## **Generated Files**

When you run:

```bash
ssh-keygen -t ed25519
```

You get:

- **Private key**: `id_ed25519` (Keep this file secret)
    
- **Public key**: `id_ed25519.pub` (Share this with remote servers)
    

> Never share your private key. It gives full access to your SSH identity.

---

## **Public Key Authentication with SSH**

### **Key Authentication Process**

1. Generate a key pair on your local machine using `ssh-keygen`.
    
2. Copy the public key to the remote server’s `~/.ssh/authorized_keys` using:
    
    ```bash
    ssh-copy-id user@remote-host
    ```
    
3. Now you can log in without entering your password:
    
    ```bash
    ssh user@remote-host
    ```
    

### **Specifying a Key Manually**

```bash
ssh -i ~/.ssh/id_ed25519 user@host
```

### **File Permissions**

Private key files must have restrictive permissions:

```bash
chmod 600 ~/.ssh/id_ed25519
```

Otherwise, SSH will ignore the key for security reasons.

---

## **SSH in CTFs and Penetration Testing**

- Attackers often **leave their public key in `~/.ssh/authorized_keys`** on compromised machines for persistent access.
    
- This allows stable and full-featured access (interactive shell, tab-completion) compared to unstable reverse shells.
    

---

## **Attacking SSH Keys**

### **Brute-Force or Passphrase Cracking**

If an SSH private key is protected by a passphrase, it can be attacked using tools like **John the Ripper**.

Steps:

1. Convert the key format using `ssh2john`:
    
    ```bash
    ssh2john id_rsa > hash.txt
    ```
    
2. Run John the Ripper:
    
    ```bash
    john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
    ```
    

This emphasizes the need for strong, unique passphrases for SSH keys.

---

## **PGP / GPG (Pretty Good Privacy)**

### **What is GPG?**

**GnuPG (GPG)** is a free and open-source implementation of the **OpenPGP** standard used for:

- File encryption
    
- Email encryption
    
- Digital signatures
    

### **Key Features**

- Uses **public-private key pairs**.
    
- Encrypt files with recipient’s public key.
    
- Decrypt files with your own private key.
    

---

## **Generating a GPG Key Pair**

### Step-by-Step:

```bash
gpg --full-generate-key
```

1. Select key type:
    
    - `(9) ECC (sign and encrypt)` is recommended.
        
2. Choose a curve:
    
    - `(1) Curve25519` (Default and recommended)
        
3. Set expiration date (or choose no expiration).
    
4. Enter user information:
    
    - Real name, email address, comment.
        
5. Set a **strong passphrase** to protect your private key.
    

### Sample Output:

```
pub   ed25519 2025-08-29 [SC]
      AB7E6AA87B6A8E0D159CA7FFE5E63DBD5F83D5ED
uid           Strategos <strategos@tryhackme.thm>
sub   cv25519 2025-08-29 [E]
```

---

## **Using GPG in Practice**

### **Export Public Key**

To share your public key:

```bash
gpg --export -a strategos@tryhackme.thm > public.key
```

### **Import Someone’s Key**

```bash
gpg --import theirkey.pub
```

### **Encrypt a File**

```bash
gpg -e -r strategos@tryhackme.thm secret.txt
```

### **Decrypt a File**

```bash
gpg --decrypt secret.txt.gpg
```

### **Import Key from Backup**

```bash
gpg --import backup.key
```

---

## **Cracking GPG Keys**

If the private key is passphrase-protected and leaked:

1. Extract hash using `gpg2john`:
    
    ```bash
    gpg2john private.key > hash.txt
    ```
    
2. Crack it with John:
    
    ```bash
    john hash.txt --wordlist=rockyou.txt
    ```
    

---

## **Important Cryptography Concepts**

|Term|Description|
|---|---|
|**Cryptography**|Science of securing data through encryption.|
|**Cryptanalysis**|Study of breaking or bypassing cryptographic systems.|
|**Brute-force attack**|Trying all possible combinations to guess a password/key.|
|**Dictionary attack**|Trying known words (from dictionaries) to guess passwords or keys.|

---

## **Best Practices for Secure Key Usage**

- Always use **strong passphrases**.
    
- Never share your **private key**.
    
- Backup your private keys **securely** (external encrypted drive).
    
- Regularly rotate and audit keys.
    
- Use **hardware security keys** where possible (YubiKey, Nitrokey).
    

---
### **What is a Hash Function?**

A **hash function** is a **one-way function** that takes an input (or message) of arbitrary length and returns a fixed-size string, known as a **hash**, **digest**, or **checksum**.

#### **Key Characteristics:**

- **Deterministic**: Same input always gives the same output.
    
- **Fixed Size**: Output size is always fixed (e.g., 128 bits for MD5, 160 for SHA-1, 256 for SHA-256).
    
- **Efficient**: Fast to compute.
    
- **Irreversible**: It should be computationally infeasible to reverse the hash and find the original input.
    
- **Avalanche Effect**: A small change in input leads to a drastically different output.
    
- **Collision-Resistant**: No two different inputs should produce the same hash (although collisions are mathematically inevitable, they must be hard to find).
    

---

### **Example – Hashing Files with Small Changes**

Create two files:

```bash
echo -n T > file1.txt
echo -n U > file2.txt
```

Hexdump both:

```bash
hexdump -C file1.txt   # Output: 54
hexdump -C file2.txt   # Output: 55
```

Hash them using different algorithms:

```bash
md5sum file1.txt file2.txt
sha1sum file1.txt file2.txt
sha256sum file1.txt file2.txt
```

Even though only **1 bit differs**, the hash values are **completely different**, demonstrating the **avalanche effect**.

---

### **Why is Hashing Important?**

### **Applications:**

- **Password Security**: Storing only the hash of the password, not the password itself.
    
- **Data Integrity**: Checking that files haven’t changed (e.g., verifying downloaded files).
    
- **Digital Signatures & Certificates**
    
- **Blockchain**: Hashing is critical in blockchains for block IDs, transactions, and proof-of-work.
    

---

### **Hash Collisions**

### Definition:

A **hash collision** occurs when **two different inputs** produce the **same hash output**.

#### **Pigeonhole Principle**:

Since the number of inputs is infinite and output is finite, **collisions are mathematically inevitable**.

#### **Example**:

If a hash function outputs 4-bit hashes:

- Total unique hashes = 2⁴ = 16
    
- If we hash 21 inputs, at least 5 must **share hashes** (collision).
    

### **Insecure Hash Algorithms**

- **MD5** and **SHA1** are considered insecure due to known collision vulnerabilities.
    
    - Visit: [MD5 Collision Demo](https://www.mscs.dal.ca/~selinger/md5collision/)
        
    - Visit: [SHA1 Collision (Shattered)](https://shattered.io/)
        

---

### **Password Storage - Secure vs Insecure**

### **Insecure Practices**

1. **Plaintext Passwords**
    
    - Example: RockYou data breach (resulted in `rockyou.txt`)
        
2. **Encrypted Passwords**
    
    - Example: Adobe used deprecated encryption and stored password hints in plaintext.
        
3. **Insecure Hashing without Salt**
    
    - Example: LinkedIn stored SHA1 hashes without salts.
        

### **Secure Password Hashing Process**

1. Choose a secure hashing algorithm: `bcrypt`, `scrypt`, `PBKDF2`, or `Argon2`
    
2. Generate a **unique salt** for each user.
    
3. Combine password + salt.
    
4. Hash the combined string.
    
5. Store both the **hash** and the **salt** in the database.
    

#### Example:

- Password: `AL4RMc10k`
    
- Salt: `Y4UV*^(=go_!`
    
- Hashing `AL4RMc10kY4UV*^(=go_!)` using `bcrypt`
    
- Store:
    
    - `bcrypt_hash(AL4RMc10kY4UV*^(=go_!))`
        
    - `salt = Y4UV*^(=go_!)`
        

---

### **Rainbow Tables**

### Definition:

A **rainbow table** is a precomputed table mapping **hashes to plaintext passwords**, allowing for quick reverse lookup of hash values (no salt).

### Example Table:

|Hash|Password|
|---|---|
|e99a18c428cb38d5f260853678922e03|abc123|
|e10adc3949ba59abbe56e057f20f883e|123456|
|b0baee9d279d34fa1dfd71aadb908c3f|11111|

#### Countermeasure:

- Use **unique salts** → Break rainbow table efficiency.
    
- With unique salts, the same password will yield **different hashes**.
    

---

## **Identifying and Cracking Hashes**

### **Hash Identification**

- Use tools: `hashID`, `Hash-Identifier`
    
- Consider hash **length**, **prefix**, and **context** (web app, OS, etc.)
    

### **Hash Cracking Tools**

- **John the Ripper**: CPU-based password cracker.
    
- **Hashcat**: GPU-accelerated, flexible cracking tool.
    

### **Example Hashcat Syntax**

```bash
hashcat -m <hash_type> -a <attack_mode> hash.txt wordlist.txt
```

#### Examples:

```bash
hashcat -m 0 -a 0 md5hash.txt rockyou.txt         # MD5
hashcat -m 1000 -a 0 ntlmhash.txt rockyou.txt     # NTLM (Windows)
hashcat -m 3200 -a 0 bcrypt_hash.txt rockyou.txt  # bcrypt
```

---

### **Linux Password Hashes**

### `/etc/shadow` Format

Each line in the file:

```plaintext
username:$prefix$options$salt$hash:...
```

#### **Hash Prefixes (Most to Least Secure)**

|Prefix|Algorithm|
|---|---|
|`$y$`|yescrypt|
|`$gy$`|gost-yescrypt|
|`$7$`|scrypt|
|`$2b$`|bcrypt|
|`$6$`|sha512crypt|
|`$1$`|md5crypt|

#### Example:

```bash
strategos:$y$j9T$76UzfgEM5PnymhQ7TlJey1$/OOSg64dhfF.TigVPdzqiFang6uZA4QA1pzzegKdVm4
```

- `$y$` → yescrypt
    
- `j9T` → parameters
    
- `76Uz...` → salt
    
- `/OOS...` → hash value
    

---

## **Windows Password Hashing**

### **SAM File (Security Accounts Manager)**

- Stores **NTLM** and **LM** hashes.
    
- NTLM = Hash of password using **MD4**.
    
- LM = Legacy format (now mostly deprecated).
    

#### Dump Tools:

- `mimikatz`, `pwdump`, `secretsdump.py`
    

#### Note:

NTLM hashes look like:

```plaintext
8846f7eaee8fb117ad06bdd830b7586c
```

Use context to differentiate from MD5/other hashes of same length.

---

## **Integrity Verification with Hashing**

### Use Case:

Verifying file integrity.

#### Example:

Downloaded ISO file and its checksum:

```bash
sha256sum Fedora-Workstation.iso
# Compare with:
# SHA256 (Fedora-Workstation.iso) = 8d3cb4...
```

- If hash matches → file is intact.
    
- If not → file is corrupted or tampered.
    

---

### **HMAC – Keyed Hash Message Authentication Code**

### **Definition**:

HMAC = A **hash function** combined with a **secret key** to provide:

- **Integrity**: Ensures data is unmodified.
    
- **Authenticity**: Verifies sender has the secret key.
    

### **How HMAC Works**:

1. Pad secret key to hash block size.
    
2. XOR with `ipad` and hash with message.
    
3. XOR key with `opad`, and hash the result of step 2.
    
4. Final output is the HMAC.
    

#### Formula:

```plaintext
HMAC(K, M) = H((K ⊕ opad) || H((K ⊕ ipad) || M))
```

Where:

- `K` = Key
    
- `M` = Message
    
- `H` = Hash function (e.g., SHA256)
    

#### Example Use Case:

Authenticating API requests:

```plaintext
HMAC = HMAC-SHA256(secret_key, message)
```

Server verifies this HMAC using the same `secret_key` to ensure message hasn’t been tampered with.

---