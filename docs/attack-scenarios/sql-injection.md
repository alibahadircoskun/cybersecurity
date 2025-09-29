# SQL Injection Attack

## What is SQL Injection?

SQL Injection (SQLi) is a code injection technique that exploits vulnerabilities in an application's database layer. Attackers insert malicious SQL statements into input fields, allowing them to manipulate database queries. This can lead to unauthorized access to sensitive data, authentication bypass, data modification, or complete system compromise.

## Attack Scenario

* **OWASP Mutillidae II Server:** 192.168.1.2
* **DVWA Server:** 192.168.1.5
* **PHP Auction Server:** 192.168.1.4
* **Attacker IP Address:** 192.168.1.8

## Lab Environment

This lab uses intentionally vulnerable applications designed for security training:
* **OWASP Mutillidae II** - A deliberately vulnerable web application
* **DVWA (Damn Vulnerable Web Application)** - An educational security testing platform
* **Hack Yourself Auction (PHP)** - A vulnerable auction application for learning

## How the Attack Works

### 1. SQL Injection - Data Extraction (Mutillidae)

**Target:** OWASP Mutillidae II at 192.168.1.2

1. **Access the vulnerable page:**
   * Navigate to: `http://192.168.1.2/mutillidae`
   * Go to: **OWASP 2013 > A1 Injection (SQL) > SQLi - Extract Data > User Info (SQL)**

2. **Testing for vulnerability:**
   * Enter a single apostrophe (`'`) in the name field
   * Click **View Account Details**
   * Observe the SQL error message revealing it's a MySQL database

3. **Exploiting the vulnerability:**
   * Input: `' OR '1'='1`
   * This makes the query always evaluate to true
   * The query becomes: `SELECT * FROM accounts WHERE username='' OR '1'='1' — password=''`
   * Click **View Account Details**
   * Result: All database records are displayed, including admin credentials

### 2. SQL Injection - Authentication Bypass (Mutillidae)

**Target:** Login page in Mutillidae

1. **Access the login page:**
   * Navigate to: **OWASP 2013 > A1 Injection (SQL) > SQLi - Bypass Authentication > Login**

2. **Bypass authentication:**
   * Username: `' OR '1'='1`
   * Password: `' OR '1'='1`
   * Click **Login**
   * Result: Successfully logged in as admin without valid credentials

### 3. SQL Injection - DVWA

**Target:** DVWA at 192.168.1.5

1. **Initial setup:**
   * Navigate to: `http://192.168.1.5/dvwa/login.php`
   * Login credentials: username `admin`, password `password`
   * Set security level to **LOW**
   * Click on **SQL Injection** in the left menu

2. **Testing normal functionality:**
   * Enter User ID: `1`
   * Click **Submit**
   * Observe: Returns ID, First Name, and Surname for user 1

3. **Exploiting the vulnerability:**
   * Enter: `' OR '1'='1`
   * Click **Submit**
   * Result: Returns all users in the database

### 4. SQL Injection - PHP Auction

**Target:** Hack Yourself Auction at 192.168.1.4

1. **Access the application:**
   * Navigate to: `http://192.168.1.4`

2. **Bypass authentication:**
   * Username: `' or 1=1#`
   * Password: `' or 1=1#`
   * Click **Go!**
   * Result: Successfully logged in

3. **Access sensitive data:**
   * Click on **Your Control Panel**
   * View all user details and system information

## Common SQL Injection Payloads

```sql
# Authentication Bypass
' OR '1'='1
' OR '1'='1' --
' OR '1'='1' #
admin' --
admin' #

# Data Extraction
' OR 1=1 --
' UNION SELECT NULL, NULL, NULL --
' UNION SELECT username, password FROM users --

# Comment Indicators
-- (SQL standard comment)
# (MySQL comment)
/* */ (Multi-line comment)
```

## Understanding the Attack

**Vulnerable Query Example:**
```sql
SELECT * FROM users WHERE username='$input' AND password='$password'
```

**After Injection:**
```sql
SELECT * FROM users WHERE username='' OR '1'='1' -- ' AND password=''
```

The `--` comments out the rest of the query, and `'1'='1'` is always true, returning all records.

## Effects of SQL Injection

* **Data Breach:** Access to sensitive information (passwords, credit cards, personal data)
* **Authentication Bypass:** Logging in without valid credentials
* **Data Modification:** Inserting, updating, or deleting database records
* **Privilege Escalation:** Gaining admin access
* **Complete System Compromise:** In severe cases, executing operating system commands

## Defense Against SQL Injection

### 1. **Parameterized Queries (Prepared Statements)**
```php
// Secure PHP example
$stmt = $pdo->prepare('SELECT * FROM users WHERE username = ? AND password = ?');
$stmt->execute([$username, $password]);
```

### 2. **Input Validation**
* Whitelist acceptable input characters
* Validate data types and formats
* Reject suspicious patterns

### 3. **Least Privilege Principle**
* Database accounts should have minimum necessary permissions
* Separate accounts for different operations

### 4. **Web Application Firewalls (WAF)**
* Deploy WAF to filter malicious requests
* Use rule sets to detect SQL injection patterns

### 5. **Regular Security Testing**
* Perform penetration testing
* Use automated vulnerability scanners
* Code reviews focusing on database interactions

### 6. **Error Handling**
* Never display detailed database errors to users
* Log errors securely for administrative review

### 7. **ORM (Object-Relational Mapping)**
* Use frameworks that abstract database queries
* Examples: Hibernate, Entity Framework, Django ORM

## Detection Methods

* Monitor for unusual query patterns in database logs
* Look for SQL syntax in input fields
* Track multiple failed authentication attempts
* Use IDS/IPS to detect SQL injection signatures
* Analyze web server logs for suspicious requests
