# DHCP Starvation Attack

## What is DHCP Starvation?

A DHCP starvation attack is a denial-of-service (DoS) attack targeting DHCP servers. The attacker floods the DHCP server with fake DHCP DISCOVER requests using spoofed MAC addresses until the server exhausts its pool of available IP addresses. As a result, legitimate clients cannot obtain an IP lease and are unable to connect to the network.

## Attack Scenario

* **DHCP Server IP Address:** 192.168.1.1
* **Attacker IP Address:** 192.168.1.8

## How the Attack Works

1. **Before the Attack:**

   * The DHCP server has a pool of IP addresses available for clients.
   * Only a few IP addresses are in use.
   * Verify DHCP leases and bindings.

2. **Starting the Attack:**

   * Launch Yersinia graphically on Kali Linux:

     ```bash
     yersinia -G
     ```
   * Navigate to the **DHCP** tab in Yersinia.
   * Launch the attack by selecting **Sending DISCOVER packet** → **OK**.
   * Alternatively, use a DHCP starvation tool:

     ```bash
     dhcpstarv -i eth0
     ```

3. **After the Attack:**

   * The attacker floods the DHCP server with bogus DISCOVER requests.
   * The DHCP server allocates all available IP addresses to the attacker’s spoofed MACs.
   * No IP addresses remain for legitimate clients.

## Commands Summary

```bash
# Start Yersinia with GUI
yersinia -G

# Run DHCP starvation from CLI
dhcpstarv -i eth0
```

## Effects of DHCP Starvation

* Legitimate clients cannot obtain IP addresses.
* Denial of service for users trying to access the network.
* Attacker may combine with a **Rogue DHCP Server** attack to provide malicious configurations.

## Defense Against DHCP Starvation

* Enable **DHCP Snooping** on switches to limit untrusted DHCP messages.
* Configure **port security** to limit the number of MAC addresses per port.
* Monitor DHCP server logs for abnormal DISCOVER/REQUEST activity.
* Use rate-limiting and access control lists (ACLs) to reduce malicious traffic.
