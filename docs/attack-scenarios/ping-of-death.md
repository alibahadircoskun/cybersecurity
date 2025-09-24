# Ping of Death Attack

## What is a Ping of Death?

A Ping of Death (PoD) attack is a type of denial-of-service (DoS) attack. The attacker sends an ICMP packet that exceeds the maximum allowed size for IP packets, causing the target machine to malfunction, freeze, or crash. While the classic PoD is rare today, similar attacks like ICMP floods are more common.

## Lab Environment

* **PC1 (Target)**: 192.168.1.9
* **PC2**: 192.168.1.10
* **Attacker Machine**: Kali Linux

### IP Configuration

**PC1:**

```bash
set pcname PC1
ip 192.168.1.9/24 192.168.1.1
save
```

**PC2:**

```bash
set pcname PC2
ip 192.168.1.10/24 192.168.1.1
save
```

## How the Attack Works

1. **Baseline Check:**

   * Test normal connectivity with a standard ping from PC1 to PC2:

     ```bash
     ping 192.168.1.10
     ```
   * Ensure that the connection works and the target responds normally.

2. **Executing the Ping of Death:**

   * From the attacker machine, send an oversized ICMP packet:

     ```bash
     ping 192.168.1.9 -s 65500 -t 1
     ```
   * `-s 65500` specifies the packet size near the maximum limit.
   * `-t 1` sets the TTL (time-to-live).

3. **Observation:**

   * The target machine (PC1) may freeze or crash due to the oversized packet.

## Capturing the Attack

* Use Wireshark to capture packets and observe fragmented ICMP packets.
* Analyze how the IP layer fragments oversized packets before delivery.

## Effects of the Attack

* Denial of service on the target machine.
* Potential crashes or freezes.
* Disruption of network communication.

## Defensive Measures

* Configure firewalls to block oversized ICMP packets.
* Apply OS patches and updates to prevent vulnerabilities.
* Monitor ICMP traffic for unusual patterns.
* Use intrusion detection systems (IDS) to detect ICMP flood or PoD attempts.
