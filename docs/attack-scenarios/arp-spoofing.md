# ARP Spoofing Attack

## What is ARP Spoofing?

ARP spoofing (also known as ARP poisoning) is a type of Man-in-the-Middle (MITM) attack where an attacker sends falsified ARP messages over a local network. This causes the victim machines to associate the attacker’s MAC address with the IP address of a legitimate host, allowing the attacker to intercept, modify, or block network traffic.

## Attack Scenario

* **Web-SRV1 IP Address:** 192.168.1.6
* **Web-SRV2 IP Address:** 192.168.1.7
* **Attacker IP Address (Kali Linux):** 192.168.1.8

## How the Attack Works

1. **Preparation:**

   * Ensure Web-SRV2’s web services are running (e.g., via XAMPP).

     ```text
     Start → XAMPP → XAMPP Control Panel → Start Apache/MySQL
     ```
   * Start Ettercap graphically on Kali Linux:

     ```bash
     ettercap -G
     ```
   * Enable Unified Sniffing and scan for hosts.
   * Add the target systems to Target 1 and Target 2 in Ettercap.

2. **ARP Poisoning Attack:**

   * Start ARP poisoning from Ettercap and select "Sniff Remote Connections."
   * After the attack, Web-SRV1 and Web-SRV2 will display the attacker’s MAC address instead of each other’s.
   * Verify by pinging between Web-SRV1 and Web-SRV2 while monitoring traffic.

3. **Traffic Interception:**

   * Start Wireshark on the attacker (Kali Linux) to capture ICMP and HTTP packets.
   * Example observation:

     * Attacker sees ICMP packets sent from Web-SRV1 to Web-SRV2.
     * Attacker also captures HTTP packets between Web-SRV1 and Web-SRV2.

4. **Alternative Method (Command-Line ARP Spoofing):**

   * Enable IP forwarding on Kali Linux to act as a router:

     ```bash
     echo 1 > /proc/sys/net/ipv4/ip_forward
     ```
   * Run arpspoof to poison ARP tables:

     ```bash
     arpspoof -i eth0 -t 192.168.1.7 -r 192.168.1.6
     ```
   * Repeat the command swapping targets if needed.

## Commands Summary

```bash
# Graphical attack
ettercap -G

# Enable IP forwarding
echo 1 > /proc/sys/net/ipv4/ip_forward

# ARP spoofing via CLI
arpspoof -i <interface> -t <Target-IP-1> -r <Target-IP-2>
```

## Effects of ARP Spoofing

* Traffic between victims can be intercepted by the attacker.
* Sensitive information (credentials, HTTP data) may be captured.
* Network disruption or man-in-the-middle modifications are possible.

## Defense Against ARP Spoofing

* Use static ARP entries on critical servers.
* Implement dynamic ARP inspection (DAI) on switches.
* Monitor network traffic for unusual ARP activity.
* Use encrypted protocols (HTTPS, SSH) to protect data integrity.
