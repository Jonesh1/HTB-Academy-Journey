# HTB-Academy-Journey

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



### 📅 21/06/2026 - Targeted Credential Extraction & Hash Cracking with SQLMap

**Objective:** Conducted a targeted data exfiltration attack on an SQL injection vulnerability (Case #1) to isolate and extract specific user credentials without dumping unnecessary database overhead.

**Key Concepts Learned:**
* **Targeted Database Auditing:** Transitioning from broad network scanning to precise database, table, and column targeting (`-D`, `-T`, `-C`) to minimize footprint and noise.
* **Automated Hash Cracking:** Leveraging SQLMap's native dictionary-attack engine to recognize, isolate, and crack password hashes on-the-fly during the dump phase.

---

### 💻 Lab Walkthrough & Commands Used

#### Task 2: Extracting Target User Credentials (User: "Kimberly")
When auditing complex database environments, dumping entire tables leaks unnecessary data and triggers security alerts. Based on standard HackTheBox lab architecture, I focused my attack path directly on the `testdb` database and the `users` table.

1. **Enumerating Column Layout:**
   To ensure accurate data extraction, I first mapped the column headers inside the target table to identify where the usernames and passwords were stored.
   ```bash
   sqlmap -u "[http://154.57.164.70:30903/case1.php?id=1](http://154.57.164.70:30903/case1.php?id=1)" -D testdb -T users --columns --batch
