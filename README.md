# Cybersecurity Lab - Network Security Testing Environment

##  Overview
This repository contains a comprehensive cybersecurity laboratory setup for educational purposes, designed to demonstrate various network attacks and security vulnerabilities in a controlled environment.

![Topology](/docs/cybersecurity.png)


##  Lab Architecture

### Network Topology
- **External Subnet**: 192.168.100.0/24
- **Internal Subnet**: 192.168.1.0/24
- **Gateway**: 192.168.1.1 (EDGE Router)
- **DNS Servers**: Primary 8.8.8.8, Secondary 1.1.1.1

### Infrastructure Components

#### Core Network Devices
| Device | IP Address | MAC Address | Image/OS |
|--------|------------|-------------|----------|
| EDG Router | 192.168.1.1 | AA:AA:AA:AA:AA:11 | C7200-adventerprisek9_sna-mz.150-1.M |
| Core Switch | - | - | I86bi_linux_l2-ipbasek9-high_iron_aug9_2017b.bin |
| AS1 Switch | - | - | I86bi_linux_l2-ipbasek9-high_iron_aug9_2017b.bin |
| AS2 Switch | - | - | I86bi_linux_l2-ipbasek9-high_iron_aug9_2017b.bin |
| AS3 Switch | - | - | I86bi_linux_l2-ipbasek9-high_iron_aug9_2017b.bin |

#### Vulnerable Servers
| Server | IP Address | MAC Address | Purpose |
|--------|------------|-------------|---------|
| OWASP | 192.168.1.2 | AA:AA:AA:AA:AA:22 | OWASP Broken Web Apps VM 1.2 |
| BWAPP | 192.168.1.3 | AA:AA:AA:AA:AA:33 | bee-box v1.6 |
| PHPAC | 192.168.1.4 | AA:AA:AA:AA:AA:44 | PHP Auction 2.1 |
| Metasploitable | 192.168.1.5 | AA:AA:AA:AA:AA:55 | Metasploitable 2 |
| Web-SRV1 | 192.168.1.6 | AA:AA:AA:AA:AA:66 | Linux TinyCore 6.4 |
| Web-SRV2 | 192.168.1.7 | AA:AA:AA:AA:AA:77 | Windows XP SP2 |

#### Client Devices
| Device | IP Assignment | MAC Address | OS |
|--------|---------------|-------------|-----|
| PC1 | DHCP | - | VPCS |
| PC2 | DHCP | - | VPCS |
| PC3 | DHCP | AA:AA:AA:AA:AA:30 | Linux Slax 9.11.0 |
| Remote-PC1 | DHCP | AA:AA:AA:AA:AA:99 | Linux Slax 9.11.0 |
| Attacker | 192.168.1.8 | AA:AA:AA:AA:AA:88 | Kali Linux 2022.1 |

## Attack Scenarios Covered

### 1. Network Layer Attacks
- **MAC Flooding**: Switch table overflow attacks
- **Ping of Death**: Oversized ICMP packet attacks
- **ICMP Flooding**: Network congestion via ICMP
- **SYN Flooding**: TCP connection exhaustion
- **Smurf Attack**: Amplified ICMP reflection
- **LAND Attack**: Same source/destination IP spoofing

### 2. Application Layer Attacks
- **Slowloris**: HTTP connection exhaustion
- **SQL Injection**: Database manipulation attacks
- **Command Injection**: OS command execution
- **Password Attacks**: Credential compromise techniques

### 3. Network Protocol Attacks
- **ARP Spoofing**: Address resolution poisoning
- **DHCP Starvation**: IP address pool exhaustion
- **DHCP Spoofing**: Rogue DHCP server attacks
- **DNS Spoofing**: Domain name resolution manipulation

### 4. Social Engineering
- **Phishing**: Credential harvesting techniques

## Repository Structure

```
cybersecurity-lab/
├── README.md
├── docs/
│   ├── network-topology.md
│   └── attack-scenarios/
│       ├── reconnaissance.md
│       ├── mac-flooding.md
│       ├── ping-of-death.md
│       ├── icmp-flooding.md
│       ├── syn-flooding.md
│       ├── smurf-attack.md
│       ├── land-attack.md
│       ├── slowloris.md
│       ├── arp-spoofing.md
│       ├── dhcp-starvation.md
│       ├── dhcp-spoofing.md
│       ├── dns-spoofing.md
│       ├── password-attacks.md
│       ├── phishing.md
│       ├── sql-injection.md
│       └── command-injection.md
```
