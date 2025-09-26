# DHCP Spoofing Attack

## What is DHCP Spoofing?

DHCP spoofing is a network attack where an attacker sets up a rogue DHCP server on the network to provide false IP configuration to clients. This usually follows a **DHCP starvation attack**, where the attacker exhausts the legitimate DHCP server’s IP pool. Once in place, the rogue DHCP server assigns IP addresses, gateways, and DNS settings that redirect traffic through the attacker’s system, enabling Man-in-the-Middle (MiTM) attacks.

## Attack Scenario

* **DHCP Server IP Address:** 192.168.1.1
* **Attacker IP Address:** 192.168.1.8

## How the Attack Works

1. **Preparation:**

   * First, perform a DHCP starvation attack to exhaust the legitimate DHCP server:

     ```bash
     dhcpstarv -i eth0
     ```

2. **Starting Ettercap for DHCP Spoofing:**

   * Launch Ettercap graphically on Kali Linux:

     ```bash
     ettercap -G
     ```
   * Enable **Unified Sniffing**.
   * In the **MITM menu**, select **DHCP Spoofing**.

3. **Configuring the Spoofed IP Pool:**

   * Enter the spoofed IP range, for example: `192.168.1.100–200`.
   * Netmask: use the subnet mask of the attacker’s network.
   * DNS Server: attacker’s IP address (192.168.1.8 in this case).
   * Click **OK** to launch the DHCP spoofing attack.

4. **After the Attack:**

   * Renew the IP address on the victim machine.
   * The victim receives an IP address from the attacker’s pool, and the default gateway is set to the attacker’s IP address.
   * Verify in Ettercap that the victim has obtained the spoofed IP address and gateway.

## Commands Summary

```bash
# Launch Ettercap with GUI
ettercap -G

# Run DHCP starvation attack first
dhcpstarv -i eth0
```

## Effects of DHCP Spoofing

* Victims receive false IP configuration from the attacker.
* Attacker can intercept, redirect, or block network traffic.
* Default gateway and DNS can be manipulated for further attacks.

## Defense Against DHCP Spoofing

* Enable **DHCP Snooping** on switches to allow DHCP offers only from trusted ports.
* Use **port security** to restrict the number of MAC addresses per port.
* Deploy **IP Source Guard** and **Dynamic ARP Inspection (DAI)** with DHCP Snooping.
* Monitor DHCP server logs for abnormal lease behavior.
