# Methodology

## Overview

This assessment followed a standard penetration-testing methodology adapted to a single-host lab engagement against Metasploitable 2. Only phases that were actually performed are documented below.

## Phases Performed

### 1. Reconnaissance

**Objective**  
Identify live hosts on the local network segment.

**Tools**  
- Nmap  
- ifconfig

**Actions**  
- Performed a ping sweep of the 10.10.1.0/24 subnet.  
- Confirmed the attacker’s own IP address.

**Commands**
```bash
nmap 10.10.1.0/24 -sn -T4
ifconfig
```

**Results**  
Three hosts responded: 10.10.1.2, 10.10.1.3 (attacker), and 10.10.1.4 (target).

**Evidence**  
[01-network-discovery-and-ifconfig.png](../evidence/screenshots/01-network-discovery-and-ifconfig.png)

**Security Relevance**  
Host discovery establishes the attack surface and confirms network reachability.

---

### 2. Scanning & Enumeration

**Objective**  
Discover open ports, running services, and software versions on the target.

**Tools**  
- Nmap (with -sV -A)

**Actions**  
- Full TCP port scan (ports 0–65535) with version detection and OS/script scanning against 10.10.1.4.

**Commands**
```bash
sudo nmap 10.10.1.4 -Pn -sV -A -T4 -p0-65535
```

**Results**  
Numerous open ports and outdated services were identified, including vsftpd 2.3.4 on port 21. See [enumeration.md](enumeration.md) for the complete list.

**Evidence**  
- [02-nmap-service-scan-part1.png](../evidence/screenshots/02-nmap-service-scan-part1.png)  
- [03-nmap-service-scan-part2.png](../evidence/screenshots/03-nmap-service-scan-part2.png)  
- [04-nmap-service-scan-part3.png](../evidence/screenshots/04-nmap-service-scan-part3.png)

**Security Relevance**  
Version information enables precise vulnerability matching.

---

### 3. Vulnerability Identification

**Objective**  
Match discovered services to known vulnerabilities.

**Actions**  
- Observed vsftpd 2.3.4 banner and anonymous login capability.  
- Recognized the well-documented backdoor present in that specific version.

**Results**  
Confirmed presence of the vsftpd 2.3.4 backdoor (CVE-related historical issue introduced June–July 2011).

---

### 4. Exploitation

**Objective**  
Obtain a command shell on the target.

**Tools**  
- Metasploit Framework

**Actions**  
- Searched for the vsftpd module.  
- Configured and launched `exploit/unix/ftp/vsftpd_234_backdoor`.

**Commands** (inside msfconsole)
```
search vsftpd
use 0
info 0
set RHOSTS 10.10.1.4
exploit
```

**Results**  
Successful root shell obtained (uid=0(root) gid=0(root)).

**Evidence**  
- [05-metasploit-vsftpd-search-and-info.png](../evidence/screenshots/05-metasploit-vsftpd-search-and-info.png)  
- [06-vsftpd-backdoor-exploitation.png](../evidence/screenshots/06-vsftpd-backdoor-exploitation.png)

**Security Relevance**  
Demonstrates how a single outdated service can lead to complete system compromise.

---

### 5. Post-Exploitation

**Objective**  
Gather system information, demonstrate impact, and practice persistence/cleanup.

**Actions**  
- Enumerated users via `/etc/passwd` and `/etc/shadow`.  
- Created a temporary privileged user.  
- Removed the temporary user.

**Results**  
Full visibility into local accounts and password hashes. Persistence was demonstrated and then cleaned up.

**Evidence**  
- [07-root-shell-and-passwd-dump.png](../evidence/screenshots/07-root-shell-and-passwd-dump.png)  
- [08-shadow-file-and-user-enumeration.png](../evidence/screenshots/08-shadow-file-and-user-enumeration.png)  
- [09-persistence-backdoor-user-and-cleanup.png](../evidence/screenshots/09-persistence-backdoor-user-and-cleanup.png)

---

### 6. Reporting

Documentation of findings, impact, and remediation recommendations was produced (this repository).

## Phases Not Performed

- Web application testing / directory brute-forcing  
- Database-specific exploitation  
- Network pivoting  
- Privilege escalation (unnecessary – root was obtained immediately)  
- Social engineering  
- Physical testing

These were outside the scope of the focused lab exercise.
