# ICMP Flooding Attack 

## What is ICMP Flooding?

ICMP Flooding is a type of denial-of-service (DoS) attack where the attacker sends a large number of ICMP Echo Request packets (ping) to a target system, overwhelming it and causing performance degradation or a crash. Unlike a single Ping of Death, ICMP flooding uses high-volume continuous traffic to exhaust system resources.

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
   * Ensure the connection is functioning normally.

2. **Executing the ICMP Flood:**

   * From the attacker machine, use **hping3** to flood the target:

     ```bash
     hping3 -1 --flood 192.168.1.9
     ```
   * `-1` specifies ICMP mode.
   * `--flood` sends packets as fast as possible.

3. **Optional Spoofing:**

   * Spoof the source IP to appear as another host:

     ```bash
     hping3 -1 --flood -a 192.168.1.1 192.168.1.9
     ```
   * `-a` sets the source IP to a different address.

## Observing the Attack

* The target machine (PC1) may freeze, crash, or respond slowly.
* Capture packets using Wireshark to analyze the ICMP traffic volume.
* Observe the continuous stream of ICMP Echo Requests overwhelming the system.

## Effects of ICMP Flooding

* Denial of service on the target system.
* Increased CPU and network usage.
* Potential temporary network outages.

## Defense Against ICMP Flooding

* Limit ICMP traffic using firewalls.
* Rate-limit ICMP packets on network devices.
* Monitor network traffic for unusual ICMP patterns.
* Implement intrusion detection systems (IDS) to detect ICMP floods.
