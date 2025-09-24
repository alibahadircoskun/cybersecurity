# SYN Flooding Attack

## Introduction
A SYN flood is a type of Denial-of-Service (DoS) attack that exploits the TCP three-way handshake. Normally, when a client connects to a server, it sends a SYN packet, the server responds with a SYN-ACK, and the client replies with an ACK to complete the handshake. In a SYN flood, the attacker sends a large number of SYN requests but never completes the handshake, leaving the server with half-open connections. This quickly consumes server resources and prevents legitimate users from connecting.

---

## Attack Scenario

### Target
- **Web-SRV2**  
  IP: `192.168.1.7`  
  Running web services via XAMPP

### Attacker
- **Kali Linux**  
  IP: `192.168.1.8`

---

## Normal Operation
Before the attack:
- Web-SRV2 has web services running (Apache, MySQL).
- Remote-PC1 can connect to `http://192.168.1.7` in a browser without issues.

---

## Launching the Attack
On the attacker machine, use **hping3** to flood the target with SYN packets:

```bash
hping3 -V -c 1000 -d 100 -S -p 80 --flood 192.168.1.7
```

### Explanation of Flags
- `-V` : Verbose output  
- `-c 1000` : Number of packets to send  
- `-d 100` : Data size of each packet (100 bytes)  
- `-S` : Sets the SYN flag  
- `-p 80` : Target port (HTTP)  
- `--flood` : Send packets as fast as possible  

---

## Observed Effect
- Web-SRV2 becomes unresponsive and eventually unreachable.  
- Remote-PC1 can no longer load the web page.  
- The server’s resources are tied up with half-open TCP connections.

---

## Packet Capture
When capturing traffic with Wireshark:
- Numerous SYN packets appear targeting port 80 of Web-SRV2.
- No proper completion of the TCP handshake is visible.
- The flood overwhelms the TCP stack.

---

## Defensive Measures
- **SYN cookies**: Help validate legitimate connections.  
- **Rate limiting**: Control SYN packet rates per client.  
- **Firewalls/IPS**: Detect and mitigate abnormal SYN traffic.  
- **Load balancers**: Distribute requests to prevent overload.  

---

## Summary
The SYN flooding attack demonstrates how attackers can exploit the TCP handshake process to exhaust server resources. By overwhelming a server with half-open connections, attackers can deny service to legitimate users. Understanding this attack highlights the importance of monitoring traffic patterns and applying defensive strategies like SYN cookies and firewalls.
