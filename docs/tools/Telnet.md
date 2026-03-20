# Telnet

### **Understanding Telnet**

**Telnet** (port **23**) is a text-based protocol used to connect to remote systems. It provides a command-line interface for communication with other servers or services over TCP. Though it's largely replaced by SSH due to lack of encryption, it's still useful for **manual testing**, **service probing**, and **understanding protocols**.

> Telnet is commonly used to simulate client requests to test whether a server responds correctly on certain ports.

---

## Using **Telnet** to Interact with Protocols

---

### 1. **Echo Server** (Port **7**)

####  Purpose:

Echoes whatever data you send.

####  Example:

```bash
telnet example.com 7
```

Then type:

```
Hello World!
```

The server will return:

```
Hello World!
```

---

### 2. **Daytime Server** (Port **13**)

#### Purpose:

Returns current date and time as plain text.

#### Example:

```bash
telnet time.nist.gov 13
```

Output:

```
58969 21-08-03 11:26:33 50 0 0 762.1 UTC(NIST)
```

---

### 3. **HTTP Server** (Port **80**)

#### Purpose:

Used to interact with web servers.

#### Commands and Examples:

```bash
telnet example.com 80
```

##### - GET request:

```
GET / HTTP/1.1
Host: example.com

```

(Press Enter twice)  
Server responds with HTML page.

##### - POST (simulate form submission):

```
POST /form HTTP/1.1
Host: example.com
Content-Length: 13
Content-Type: application/x-www-form-urlencoded

name=Subrat
```

##### - PUT:

Creates or replaces a resource

```
PUT /file.txt HTTP/1.1
Host: example.com
Content-Length: 11

Hello World
```

##### - DELETE:

Deletes a resource

```
DELETE /file.txt HTTP/1.1
Host: example.com
```

---

### 4. **FTP Server** (Port **21**)

#### Purpose:

Used for file transfer.

#### Example:

```bash
telnet ftp.example.com 21
```

##### Commands:

```
USER yourname
PASS yourpassword
RETR test.txt     <-- Download file
STOR upload.txt   <-- Upload file
```

---

### 5. **SMTP Server** (Port **25**)

#### Purpose:

Send emails.

#### Example:

```bash
telnet smtp.example.com 25
```

##### Commands:

```
HELO yourdomain.com
MAIL FROM:<sender@example.com>
RCPT TO:<receiver@example.com>
DATA
Subject: Test Email

This is a test message.
.
QUIT
```

---

### 6. **POP3 Server** (Port **110**)

#### Purpose:

Retrieve emails from the server (inbox download).

#### Example:

```bash
telnet pop.example.com 110
```

##### Commands:

```
USER yourname
PASS yourpassword
STAT
LIST
RETR 1
DELE 1
QUIT
```

---

### 7. **IMAP Server** (Port **143**)

#### Purpose:

Access and manage mailbox on the server.

#### Example:

```bash
telnet imap.example.com 143
```

##### Commands:

```
a LOGIN yourname yourpassword
b SELECT INBOX
c FETCH 1 BODY[]
d COPY 1 "Archive"
e LOGOUT
```

---

## Summary Table

|Protocol|Default Port|Telnet Usage|Main Purpose|
|---|---|---|---|
|Echo|7|`telnet host 7`|Echo input|
|Daytime|13|`telnet host 13`|Get date & time|
|HTTP|80|`telnet host 80`|Web page requests (GET/POST/PUT/DELETE)|
|FTP|21|`telnet host 21`|File transfer|
|SMTP|25|`telnet host 25`|Send emails|
|POP3|110|`telnet host 110`|Retrieve inbox messages|
|IMAP|143|`telnet host 143`|Manage mailbox folders|

---

## Additional Notes:

- **Telnet won't work** with encrypted (SSL/TLS) services like HTTPS (port 443), FTPS, or SMTPS.
    
- Useful for **manual protocol testing**, learning, or network debugging.
    
- Use **Ctrl + ]** followed by `quit` to exit Telnet.
    

---