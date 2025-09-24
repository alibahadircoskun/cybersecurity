# Land Attack

## Introduction
A Land Attack is a type of Denial-of-Service (DoS) attack where the attacker sends a spoofed TCP SYN packet with the target’s IP address as both the **source** and the **destination**. When the target machine receives this packet, it tries to respond to itself, causing confusion in the TCP/IP stack. This can lead to high CPU usage, instability, and even a system crash.

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
- Web-SRV2 runs normally with web services active.  
- Remote clients can connect to `http://192.168.1.7` without issues.  

---

## Launching the Attack
On the attacker machine, run the following command:

```bash
hping3 -S -p 80 192.168.1.7 -a 192.168.1.7 --flood
```

### Explanation of Flags
- `-S` : Sets the SYN flag  
- `-p 80` : Target port (HTTP)  
- `192.168.1.7` : Target IP (also spoofed as source)  
- `-a 192.168.1.7` : Spoof the source address as the target itself  
- `--flood` : Send packets as fast as possible  

---

## Observed Effect
- Wireshark shows SYN packets where both the **source** and **destination** IP are the same.  
- The CPU usage on Web-SRV2 spikes dramatically.  
- After a while, Web-SRV2 stops responding and becomes unreachable.  

---

## Packet Capture
When capturing packets with Wireshark:  
- SYN packets appear with identical source and destination (`192.168.1.7 → 192.168.1.7`).  
- The abnormal traffic leads to resource exhaustion.  

---

## Defensive Measures
- **Patch systems**: Modern systems are generally immune, but unpatched ones may still be vulnerable.  
- **Firewalls/IPS**: Detect and block malformed traffic.  
- **Traffic monitoring**: Spot unusual patterns like self-directed SYN packets.  

---

## Summary
The Land Attack highlights how IP spoofing can be used to crash or freeze older or unprotected systems. Although modern operating systems mitigate this vulnerability, it remains an important concept for understanding legacy DoS techniques and the evolution of security defenses.
