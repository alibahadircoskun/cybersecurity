# Reconnaissance Attack Scenario

## Overview
Reconnaissance is the initial phase of any cyber attack where the attacker gathers information about the target network, systems, and potential vulnerabilities. This phase is crucial as it determines the success of subsequent attack phases.

## Goals
- Understand passive and active reconnaissance techniques
- Learn network discovery and enumeration methods
- Practice using common reconnaissance tools
- Identify potential attack vectors through information gathering
- Master various nmap scanning techniques
- Understand steganography and evasion methods

## Setup
- **Attacker Machine**: Kali Linux (192.168.1.8)
- **Target Network**: 192.168.1.0/24
- **Primary Targets**: Web-SRV2 (192.168.1.7), Metasploitable (192.168.1.5)
- **Target Systems**: All vulnerable servers and client devices

## Prerequisites
Before starting the reconnaissance attack:
1. Ensure Web-SRV2 (192.168.1.7) is running Windows XP SP2
2. Navigate to Start → XAMPP → XAMPP Control Panel
3. Start the web services (Apache/MySQL)
4. Verify Kali Linux attacker machine (192.168.1.8) has network connectivity

## Phase 1: Network Host Discovery

### 1.1 Basic Network Scanning
**Objective**: Discover all live hosts on the target network

**Command 1: Basic Network Scan**
```bash
# From Kali Linux (192.168.1.8)
nmap 192.168.1.0/24
```

**Purpose**: Performs a basic TCP SYN scan on common ports (top 1000) for all hosts in the subnet.

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.1
Host is up (0.00023s latency).
Not shown: 999 filtered ports
PORT   STATE SERVICE
53/tcp open  domain

Nmap scan report for 192.168.1.5
Host is up (0.00041s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown

Nmap scan report for 192.168.1.7
Host is up (0.00028s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 192.168.1.8
Host is up (0.00019s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
```

### 1.2 Ping Sweep Discovery
**Objective**: Quickly identify live hosts using ICMP ping

**Command 2: Ping Sweep**
```bash
nmap -sP 192.168.1.0/24
# Alternative: nmap -sn 192.168.1.0/24 (newer syntax)
```

**Purpose**: Performs a ping sweep to identify live hosts without port scanning.

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.1
Host is up (0.00023s latency).
MAC Address: AA:AA:AA:AA:AA:11 (Unknown)
Nmap scan report for 192.168.1.5
Host is up (0.00041s latency).
MAC Address: AA:AA:AA:AA:AA:55 (Unknown)
Nmap scan report for 192.168.1.7
Host is up (0.00028s latency).
MAC Address: AA:AA:AA:AA:AA:77 (Unknown)
Nmap scan report for 192.168.1.8
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 2.95 seconds
```

## Phase 2: Operating System Detection

### 2.1 OS Fingerprinting - Web-SRV2
**Objective**: Identify the operating system of Web-SRV2

**Command 3: OS Detection for Web-SRV2**
```bash
nmap -O 192.168.1.7
```

**Purpose**: Uses TCP/IP stack fingerprinting to determine the operating system.

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.7
Host is up (0.00028s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
MAC Address: AA:AA:AA:AA:AA:77 (Unknown)
Device type: general purpose
Running: Microsoft Windows XP
OS CPE: cpe:/o:microsoft:windows_xp::sp2
OS details: Microsoft Windows XP SP2 or SP3
Network Distance: 1 hop
```

### 2.2 OS Detection - Metasploitable
**Objective**: Identify the operating system of Metasploitable server

**Command 4: OS Detection for Metasploitable**
```bash
nmap -O 192.168.1.5
```

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.5
Host is up (0.00041s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
[... truncated for brevity ...]
MAC Address: AA:AA:AA:AA:AA:55 (Unknown)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
```

## Phase 3: Service Version Detection

### 3.1 Service Version Scanning
**Objective**: Identify specific service versions running on target systems

**Command 5: Service Version Detection - Web-SRV2**
```bash
nmap -sV 192.168.1.7
```

**Purpose**: Probes open ports to determine service/version information.

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.7
Host is up (0.00028s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Microsoft Windows XP microsoft-ds
3389/tcp open  ms-wbt-server Microsoft Terminal Service
MAC Address: AA:AA:AA:AA:AA:77 (Unknown)
Service Info: OS: Windows XP; CPE: cpe:/o:microsoft:windows_xp

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.42 seconds
```

## Phase 4: Advanced Port Scanning Techniques

### 4.1 Stealth and Evasion Scanning
**Objective**: Use advanced scanning techniques to avoid detection

**Command 6: Hping3 Port Scanning with Source Randomization**
```bash
hping3 --scan 1-65535 192.168.1.7 -S --rand-source
```

**Purpose**: Performs SYN scan across all ports using randomized source addresses to evade detection.

**Expected Output**:
```
Scanning 192.168.1.7 (192.168.1.7), port 1-65535
65535 ports to scan, use -V to see all the replies
+----+-----------+---------+---+-----+-----+-----+
|port| serv name |  flags  |ttl| id  | win | len |
+----+-----------+---------+---+-----+-----+-----+
  135 msrpc      : .S..A...  128     0  8192    46
  139 netbios-ssn: .S..A...  128     0  8192    46
  445 microsoft-ds: .S..A... 128     0  8192    46
 3389 ms-wbt-server: .S..A.. 128     0  8192    46
All replies received. Done.
Under 1 second.
```

### 4.2 Specific Port Scanning
**Objective**: Focus on specific services of interest

**Command 7: HTTP Port Scanning**
```bash
nmap -p 80 192.168.1.7
```

**Purpose**: Checks specifically for HTTP service on port 80.

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.7
Host is up (0.00028s latency).

PORT   STATE  SERVICE
80/tcp closed http
MAC Address: AA:AA:AA:AA:AA:77 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.42 seconds
```

**Note**: Port 80 shows as closed initially. After starting XAMPP services, this should show as open.

### 4.3 Fast Scanning
**Objective**: Perform rapid scanning of common ports

**Command 8: Fast Scan**
```bash
nmap -F 192.168.1.7
```

**Purpose**: Scans only the top 100 most common ports for faster results.

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.7
Host is up (0.00028s latency).
Not shown: 96 closed ports
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
MAC Address: AA:AA:AA:AA:AA:77 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.32 seconds
```

### 4.4 Fragmented Packet Scanning
**Objective**: Use packet fragmentation to evade firewalls and IDS

**Command 9: Fragmented Scan**
```bash
nmap -f 192.168.1.7
```

**Purpose**: Fragments TCP headers over several packets to potentially bypass packet filters.

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.7
Host is up (0.00028s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
MAC Address: AA:AA:AA:AA:AA:77 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 2.95 seconds
```

## Phase 5: Network Connectivity Testing

### 5.1 ICMP Testing
**Objective**: Test basic network connectivity

**Command 10: FPing Connectivity Test**
```bash
fping 192.168.1.7
```

**Purpose**: Fast ping utility to test host availability.

**Expected Output**:
```
192.168.1.7 is alive
```

**Alternative detailed ping:**
```bash
ping -c 4 192.168.1.7
```

**Expected Output**:
```
PING 192.168.1.7 (192.168.1.7) 56(84) bytes of data.
64 bytes from 192.168.1.7: icmp_seq=1 ttl=128 time=0.284 ms
64 bytes from 192.168.1.7: icmp_seq=2 ttl=128 time=0.301 ms
64 bytes from 192.168.1.7: icmp_seq=3 ttl=128 time=0.287 ms
64 bytes from 192.168.1.7: icmp_seq=4 ttl=128 time=0.295 ms

--- 192.168.1.7 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3058ms
rtt min/avg/max/mdev = 0.284/0.291/0.301/0.007 ms
```

## Phase 6: Web Application Reconnaissance

### 6.1 Web Technology Identification
**Objective**: Identify web technologies and frameworks in use

**Command 11: Web Technology Fingerprinting - Web-SRV2**
```bash
whatweb 192.168.1.7
```

**Purpose**: Identifies web technologies, CMS, and frameworks.

**Expected Output (before XAMPP started)**:
```
http://192.168.1.7 [ERROR] getaddrinfo: No address associated with hostname
```

**Expected Output (after XAMPP started)**:
```
http://192.168.1.7 [200 OK] Apache[2.4.39], Country[RESERVED][ZZ], HTTPServer[Apache/2.4.39 (Win32) OpenSSL/1.1.1b mod_perl/2.0.8-dev Perl/v5.16.3], IP[192.168.1.7], OpenSSL[1.1.1b], Perl[v5.16.3], Title[XAMPP Apache + MariaDB + PHP + Perl], X-Powered-By[PHP/7.3.4]
```

**Command 12: Web Technology Fingerprinting - Metasploitable**
```bash
whatweb 192.168.1.5
```

**Expected Output**:
```
http://192.168.1.5 [200 OK] Apache[2.2.8], Country[RESERVED][ZZ], HTTPServer[Apache/2.2.8 ((Ubuntu) DAV/2)], IP[192.168.1.5], Title[Metasploitable2 - Linux]
```

### 6.2 Web Vulnerability Scanning
**Objective**: Identify potential web application vulnerabilities

**Command 13: Nikto Vulnerability Scan - Web-SRV2**
```bash
nikto -host 192.168.1.7
```

**Purpose**: Comprehensive web application vulnerability scanner.

**Expected Output (after XAMPP started)**:
```
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.1.7
+ Target Hostname:    192.168.1.7
+ Target Port:        80
+ Start Time:         2024-01-15 10:30:00 (GMT-5)
---------------------------------------------------------------------------
+ Server: Apache/2.4.39 (Win32) OpenSSL/1.1.1b mod_perl/2.0.8-dev Perl/v5.16.3
+ Server may leak inodes via ETags, header found with file /, inode: 1a, size: 58c4d0a2b1b00, mtime: gzip
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content in a different fashion than intended.
+ Root page / redirects to: http://192.168.1.7/xampp/
+ Apache/2.4.39 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ OSVDB-3268: /xampp/: Directory indexing found.
+ OSVDB-3268: /xampp/xampp/: Directory indexing found.
+ Server banner has changed from 'Apache/2.4.39 (Win32) OpenSSL/1.1.1b mod_perl/2.0.8-dev Perl/v5.16.3' to 'Apache'.
+ OSVDB-3268: /examples/: Directory indexing found.
+ OSVDB-6694: /.DS_Store: Apache on Mac OSX will serve the .DS_Store file, which contains sensitive information. Configure Apache to ignore this file or upgrade to a newer version.
+ OSVDB-3233: /icons/README: Apache default file found.
+ /xampp/phpmyadmin/: phpMyAdmin directory found
+ /phpmyadmin/: phpMyAdmin directory found
```

## Phase 7: Comprehensive Network Analysis

### 7.1 Complete Network Enumeration
**Objective**: Perform comprehensive reconnaissance of the entire network

**Command 14: Full Network Scan**
```bash
nmap 192.168.1.0/24
```

**Purpose**: Comprehensive TCP SYN scan of all hosts in the network.

**Expected Output**:
```
Starting Nmap 7.92 ( https://nmap.org )
Nmap scan report for 192.168.1.1
Host is up (0.00023s latency).
Not shown: 999 filtered ports
PORT   STATE SERVICE
53/tcp open  domain
MAC Address: AA:AA:AA:AA:AA:11 (Unknown)

Nmap scan report for 192.168.1.2
Host is up (0.00035s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
8080/tcp open  http-proxy
8443/tcp open  https-alt
MAC Address: AA:AA:AA:AA:AA:22 (Unknown)

Nmap scan report for 192.168.1.3
Host is up (0.00038s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
MAC Address: AA:AA:AA:AA:AA:33 (Unknown)

Nmap scan report for 192.168.1.4
Host is up (0.00031s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
80/tcp open  http
MAC Address: AA:AA:AA:AA:AA:44 (Unknown)

Nmap scan report for 192.168.1.5
Host is up (0.00041s latency).
Not shown: 977 closed ports
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: AA:AA:AA:AA:AA:55 (Unknown)

Nmap scan report for 192.168.1.6
Host is up (0.00029s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: AA:AA:AA:AA:AA:66 (Unknown)

Nmap scan report for 192.168.1.7
Host is up (0.00028s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
MAC Address: AA:AA:AA:AA:AA:77 (Unknown)

Nmap scan report for 192.168.1.8
Host is up (0.00019s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh

Nmap done: 256 IP addresses (8 hosts up) scanned in 4.87 seconds
```
## Command Reference Quick Guide

### Core Nmap Commands Used
| Command | Purpose | Stealth Level |
|---------|---------|---------------|
| `nmap 192.168.1.0/24` | Basic network scan | Medium |
| `nmap -sP 192.168.1.0/24` | Ping sweep only | Low |
| `nmap -O 192.168.1.7` | OS detection | Medium |
| `nmap -sV 192.168.1.7` | Service version detection | Medium |
| `nmap -F 192.168.1.7` | Fast scan (top 100 ports) | Medium |
| `nmap -f 192.168.1.7` | Fragmented packets | High |
| `nmap -p 80 192.168.1.7` | Specific port scan | Low |

### Alternative Tools Comparison
| Tool | Purpose | Advantage |
|------|---------|-----------|
| `nmap` | Comprehensive scanning | Feature-rich, reliable |
| `hping3` | Custom packet crafting | Stealth, evasion capabilities |
| `fping` | Fast connectivity testing | Speed, simple output |
| `whatweb` | Web technology ID | Web-specific intelligence |
| `nikto` | Web vulnerability scanning | Comprehensive web testing |

## Detection and Evasion Analysis

### IDS/IPS Detection Likelihood
| Technique | Detection Risk | Mitigation |
|-----------|----------------|------------|
| Ping Sweep | Low | Normal network behavior |
| Port Scanning | High | Use timing delays (-T2) |
| Service Scanning | Very High | Fragment packets (-f) |
| Vulnerability Scanning | Critical | Use from multiple sources |

### Evasion Techniques
1. **Fragmented Packets** (`-f`): Splits TCP headers across packets
2. **Source Randomization** (`--rand-source`): Randomizes source IPs
3. **Timing Controls** (`-T0` to `-T5`): Controls scan speed
4. **Decoy Scanning** (`-D`): Uses decoy IP addresses

## Tools Summary

### Primary Reconnaissance Tools
1. **nmap**: Network discovery and port scanning
2. **hping3**: Advanced packet crafting and scanning
3. **fping**: Fast ICMP connectivity testing
4. **whatweb**: Web technology fingerprinting
5. **nikto**: Web vulnerability assessment

### Command Syntax Reference
```bash
# Network Discovery
nmap -sn <network>                    # Ping sweep
nmap -sL <network>                    # List scan

# Port Scanning  
nmap <target>                         # Basic scan
nmap -sS <target>                     # SYN scan
nmap -sT <target>                     # Connect scan
nmap -sU <target>                     # UDP scan

# Service Detection
nmap -sV <target>                     # Version detection
nmap -O <target>                      # OS detection
nmap -A <target>                      # Aggressive scan

# Timing and Performance
nmap -T<0-5> <target>                 # Timing template
nmap --max-rate <rate> <target>       # Max packets/sec
nmap --min-rate <rate> <target>       # Min packets/sec

# Evasion Techniques
nmap -f <target>                      # Fragment packets
nmap -D <decoys> <target>             # Decoy scan
nmap --source-port <port> <target>    # Source port
nmap --data-length <length> <target>  # Random data
```

### Advanced Scanning Options
```bash
# Comprehensive enumeration
nmap -sS -sV -O -A -p- <target>

# Script scanning
nmap --script <category> <target>
nmap --script vuln <target>
nmap --script discovery <target>

# Output formats
nmap -oA <basename> <target>          # All formats
nmap -oN <file> <target>              # Normal format
nmap -oX <file> <target>              # XML format
nmap -oG <file> <target>              # Greppable format
```

## Defensive Considerations

### Detection Methods
**Network-Level Detection:**
- **IDS/IPS Signatures**: Port scan detection rules
- **Network Monitoring**: Unusual traffic patterns
- **SIEM Correlation**: Multiple connection attempts
- **Honeypots**: Decoy services to detect scanning

**Host-Level Detection:**
- **Log Analysis**: Connection attempt logging
- **Rate Limiting**: Block rapid connection attempts  
- **Port Knocking**: Hide services behind sequences
- **Fail2Ban**: Automatic IP blocking

### Mitigation Strategies
**Network Hardening:**
- **Firewalls**: Limit port exposure
- **Network Segmentation**: Isolate critical systems
- **VLANs**: Separate network segments
- **Access Control Lists**: Restrict network access

**Service Hardening:**
- **Disable Unused Services**: Reduce attack surface
- **Service Banners**: Remove version information
- **Default Accounts**: Change default credentials
- **Security Updates**: Keep systems patched

### Reconnaissance Indicators
**Red Flags in Network Logs:**
```bash
# Common reconnaissance patterns to monitor:
# - Sequential port scanning
# - Version detection attempts
# - Multiple connection failures
# - Unusual user agents in web logs
# - DNS enumeration attempts
# - SNMP community string testing
```

### Reconnaissance Methodology
1. **Start Passive**: Begin with least intrusive methods
2. **Progress to Active**: Gradually increase scanning intensity  
3. **Focus on High-Value**: Prioritize systems with multiple services
4. **Document Everything**: Keep detailed records of findings
5. **Verify Results**: Cross-reference information from multiple tools

### Tool Selection Strategy
- **nmap**: Primary tool for most reconnaissance tasks
- **hping3**: When stealth and evasion are critical
- **whatweb/nikto**: Specialized web application analysis
- **fping**: Quick connectivity verification
- **Multiple Tools**: Cross-verification of results

### Common Mistakes to Avoid
- **Too Aggressive**: High-intensity scans trigger detection
- **Incomplete Coverage**: Missing UDP services or high ports
- **Poor Documentation**: Not recording findings systematically
- **Single Tool Reliance**: Not cross-verifying results
- **Ignoring Context**: Not considering defensive measures

## Next Phase Integration

This reconnaissance provides foundation for:

**Network Attacks:**
- **MAC Flooding**: Target switch infrastructure  
- **ARP Spoofing**: Focus on active hosts discovered
- **DHCP Attacks**: Leverage network configuration knowledge

**Service-Specific Attacks:**
- **Web Applications**: Target HTTP/HTTPS services found
- **SSH/Telnet**: Credential attacks on remote access
- **Database Services**: MySQL/PostgreSQL exploitation
- **SMB/NetBIOS**: Windows-specific attacks

**Information for Planning:**
- Network topology understanding
- Service version vulnerabilities
- Target prioritization based on attack surface
- Defensive capability assessment
