# Reconnaissance

## Objective

Identify live hosts on the local network segment and confirm the attacker machine’s own network configuration.

## Activity 1 – Host Discovery

**Tool**  
Nmap

**Command**
```bash
nmap 10.10.1.0/24 -sn -T4
```

**Explanation**  
`-sn` performs a ping scan (no port scan). `-T4` sets an aggressive timing template for faster results on a local network.

**Result**
```
Starting Nmap 7.92 ...
Nmap scan report for 10.10.1.2
Host is up (0.0024s latency).
Nmap scan report for 10.10.1.3
Host is up (0.0023s latency).
Nmap scan report for 10.10.1.4
Host is up (0.0020s latency).
Nmap done: 256 IP addresses (3 hosts up) scanned in 3.12 seconds
```

**Analysis**  
Three hosts are alive on the subnet. 10.10.1.3 is the attacker machine. 10.10.1.4 is the intended target (Metasploitable 2). 10.10.1.2 was noted but not further examined in this assessment.

**Evidence**  
[01-network-discovery-and-ifconfig.png](../evidence/screenshots/01-network-discovery-and-ifconfig.png)

---

## Activity 2 – Confirm Attacker IP

**Tool**  
ifconfig (ipconfig was attempted first but is not present on Linux)

**Commands**
```bash
ipconfig          # failed – command not found
ifconfig
```

**Result (relevant excerpt)**
```
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.1.3  netmask 255.255.255.0  broadcast 10.10.1.255
        ether 08:00:27:54:c1:71
```

**Analysis**  
Attacker IP is confirmed as 10.10.1.3 on interface enp0s3. This information is later used for any reverse-connection attempts.

**Evidence**  
Same screenshot as above.

---

## Summary

Reconnaissance successfully mapped the local network and established the target IP (10.10.1.4) for subsequent scanning and exploitation phases.
