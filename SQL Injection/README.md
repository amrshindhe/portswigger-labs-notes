# SQL Injection Vulnerability

## Overview
SQL Injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. By manipulating SQL queries, an attacker can retrieve, modify, or delete database records without proper authorization.

## Types of SQL Injection
- **In-band SQL Injection**: If we are using same channel to both launch the attack and gather the result 
  - **Error-Based SQL Injection**: Uses database errors to extract information.
  - **Union-Based SQL Injection**: Leverages the `UNION` SQL operator to retrieve additional data.
- **Blind SQL Injection**: Exploits queries that do not return results but still affect database logic.
  - **Boolean-Based Blind SQLi**: Infers data by observing application behavior.
  - **Time-Based Blind SQLi**: Uses time delays to determine query success.
- **Out-of-Band SQL Injection**: Uses external systems like DNS or HTTP requests to exfiltrate data.

## Impact of a SQL Injection Attack
Depends on vulnerability, in worst case data leak, do CRUD operation, also can lead to RCE

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

### 3. Apply Least Privilege Principle
- Use a database account with minimal permissions.

### 4. Validate and Sanitize Input
- Reject unexpected characters and enforce input length restrictions.

### 5. Use Web Application Firewalls (WAFs)
- Tools like **AWS WAF** or **ModSecurity** can block common SQLi patterns.

## References
- [OWASP SQL Injection Guide](https://owasp.org/www-community/attacks/SQL_Injection)
- [PortSwigger SQLi Labs](https://portswigger.net/web-security/sql-injection)

---
This repository contains examples, test cases, and solutions related to SQL Injection vulnerabilities. Feel free to contribute and explore various SQLi scenarios! 🚀

