# MAC Flooding Attack

## What is MAC Flooding?

MAC flooding is a type of network attack that targets a switch’s MAC address table, also called the CAM table. A switch uses this table to remember which MAC addresses are connected to which ports, so it can send traffic only to the correct destination. In a MAC flooding attack, an attacker floods the switch with thousands of fake MAC addresses. When the table becomes full, the switch can’t learn new MAC addresses and starts broadcasting all traffic to all ports. Essentially, the switch starts behaving like a hub, making it easy for attackers to sniff network traffic.

## How the Attack Works

1. **Before the Attack:**

   * The switch has a limited number of MAC addresses learned from connected devices.
   * Commands to check the MAC table:

     ```
     show mac address-table count
     show mac address-table
     ```

2. **During the Attack:**

   * The attacker uses **Macof** to generate thousands of fake MAC addresses.
   * Example command:

     ```bash
     macof -i eth0
     ```
   * Macof can send roughly 155,000 random MAC addresses per minute.

3. **After the Attack:**

   * The MAC table is filled with fake addresses.
   * Legitimate traffic is broadcast to all ports.
   * The attacker can capture traffic from other devices on the network.

## Commands to Observe the Attack

```bash
# Check MAC table before attack
show mac address-table

# Run MAC flooding attack
macof -i eth0

# Check MAC table after attack
show mac address-table
```

## Effects of MAC Flooding

* Switches behave like hubs, broadcasting traffic to all ports.
* Sensitive data may be exposed to attackers.
* Network performance may degrade due to excessive MAC entries.

## Defense Against MAC Flooding

* Enable port security to limit the number of MAC addresses per port.
* Monitor switch logs and network traffic for unusual behavior.
* Segment networks to reduce the impact of attacks.
