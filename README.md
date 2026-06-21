# HTB-Academy-Journey

### 📅 21/06/2026 - Advanced Database Enumeration & Targeted Extraction (SQLMap Case #1)

**Objective:** Exploited an SQL injection vulnerability to locate hidden schema structures and selectively exfiltrate target user credentials using advanced SQLMap automation.

**Key Concepts Learned:**
* **Targeted Searching:** Utilizing the `--search` flag alongside specific identifiers to pinpoint columns across massive database schemas, bypassing unneeded noise.
* **Data Minimization & Exfiltration:** Mapping out layouts with `--columns` and isolating extraction to specific variables (`-C name,password`) to maintain a clean operational footprint.
* **Automated Hash Cracking:** Harnessing SQLMap's underlying multi-processing dictionary attack engine to identify and crack hashes seamlessly during the dump phase.

---

### 💻 Lab Walkthrough & Commands Used

#### 🔍 Task 1: Locating a Specific Column ("style")
Initially, running a broad `--schema` dump on the endpoint produced excessive, unreadable terminal output. To quickly find the target column containing the keyword "style" without auditing the entire database architecture manually, I used a targeted search filter to look for columns matching the keyword `style`:

```bash
sqlmap -u "[http://154.57.164.70:30903/case1.php?id=1](http://154.57.164.70:30903/case1.php?id=1)" --search -C style --batch
