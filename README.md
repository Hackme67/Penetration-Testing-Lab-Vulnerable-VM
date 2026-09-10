# Penetration Testing Lab – Metasploitable 2 Assessment

**Authorized Lab Exercise Only**

This repository documents a full penetration test performed against an intentionally vulnerable virtual machine (Metasploitable 2) in an isolated, authorized lab environment.

---

## Overview

This project demonstrates a practical penetration-testing engagement against Metasploitable 2. The assessment followed a structured methodology: reconnaissance, service enumeration, vulnerability identification, exploitation of the vsftpd 2.3.4 backdoor, post-exploitation activities (including credential dumping and temporary persistence), and cleanup.

All activities were conducted solely against systems under the tester’s control in a legal lab setting. No unauthorized systems were targeted.

**Objective:** Gain root-level access on the target VM, document the attack path, collect evidence, and provide remediation guidance.

---

## Lab Environment

| Component          | Details                                      |
|--------------------|----------------------------------------------|
| Attacker Machine   | Parrot OS (`zen@parrot`)                     |
| Attacker IP        | 10.10.1.3                                    |
| Target VM          | Metasploitable 2                             |
| Target IP          | 10.10.1.4                                    |
| Network            | 10.10.1.0/24 (isolated lab)                  |
| Other hosts found  | 10.10.1.2 (up)                               |
| Testing Scope      | Single target host (10.10.1.4)               |
| Authorization      | Authorized lab exercise only                 |

---

## Architecture

![Network Architecture](diagrams/network-architecture.png)

The lab consists of a Parrot OS attacker machine and the Metasploitable 2 target on the same private network segment (10.10.1.0/24). The target exposes a large number of outdated and intentionally vulnerable services.

---

## Methodology

The assessment followed these phases (only phases actually performed are listed):

1. **Reconnaissance** – Host discovery on the local subnet  
2. **Scanning** – Full TCP port scan with service/version detection  
3. **Enumeration** – Identification of running services and versions  
4. **Vulnerability Identification** – Recognition of the vsftpd 2.3.4 backdoor  
5. **Exploitation** – Metasploit module `exploit/unix/ftp/vsftpd_234_backdoor`  
6. **Post-Exploitation** – System enumeration, credential dumping, temporary persistence, cleanup  
7. **Evidence Collection & Reporting**

Privilege escalation was **not required** because the vsftpd backdoor provided an immediate root shell.

---

## Tools Used

| Tool              | Purpose                                      |
|-------------------|----------------------------------------------|
| Nmap              | Host discovery and full service/version scan |
| ifconfig          | Confirm attacker IP address                  |
| Metasploit Framework | Exploitation of vsftpd 2.3.4 backdoor     |
| Standard Linux utilities | Post-exploitation (cat, ls, useradd, etc.) |

---

## Attack Path

```
Reconnaissance (nmap -sn 10.10.1.0/24)
        ↓
Full Service Scan (nmap -sV -A -p0-65535 10.10.1.4)
        ↓
Vulnerability Identification (vsftpd 2.3.4)
        ↓
Exploitation (Metasploit vsftpd_234_backdoor)
        ↓
Root Shell (uid=0)
        ↓
Post-Exploitation (/etc/passwd, /etc/shadow, backdoor user)
        ↓
Cleanup (userdel)
```

![Attack Path](diagrams/attack-path.png)

---

## Key Findings

| ID   | Finding                              | Severity  | Impact                                      |
|------|--------------------------------------|-----------|----------------------------------------------|
| F-01 | vsftpd 2.3.4 Backdoor                | Critical  | Immediate unauthenticated root shell         |
| F-02 | Large number of outdated services    | High      | Multiple potential attack vectors            |
| F-03 | Anonymous FTP enabled                | Medium    | Information disclosure / staging point       |
| F-04 | Weak/legacy authentication services  | High      | Telnet, r-services, outdated SSH/Samba       |
| F-05 | Database services exposed            | High      | MySQL 5.0.51a and PostgreSQL 8.3 reachable   |

Full details are available in [docs/findings.md](docs/findings.md) and the formal report.

---

## Evidence

| Stage                    | Screenshot / File                                      |
|--------------------------|--------------------------------------------------------|
| Network discovery & IP   | [01-network-discovery-and-ifconfig.png](evidence/screenshots/01-network-discovery-and-ifconfig.png) |
| Nmap service scan (1/3)  | [02-nmap-service-scan-part1.png](evidence/screenshots/02-nmap-service-scan-part1.png) |
| Nmap service scan (2/3)  | [03-nmap-service-scan-part2.png](evidence/screenshots/03-nmap-service-scan-part2.png) |
| Nmap service scan (3/3)  | [04-nmap-service-scan-part3.png](evidence/screenshots/04-nmap-service-scan-part3.png) |
| Metasploit module info   | [05-metasploit-vsftpd-search-and-info.png](evidence/screenshots/05-metasploit-vsftpd-search-and-info.png) |
| Successful exploitation  | [06-vsftpd-backdoor-exploitation.png](evidence/screenshots/06-vsftpd-backdoor-exploitation.png) |
| Root shell + passwd      | [07-root-shell-and-passwd-dump.png](evidence/screenshots/07-root-shell-and-passwd-dump.png) |
| Shadow file dump         | [08-shadow-file-and-user-enumeration.png](evidence/screenshots/08-shadow-file-and-user-enumeration.png) |
| Persistence & cleanup    | [09-persistence-backdoor-user-and-cleanup.png](evidence/screenshots/09-persistence-backdoor-user-and-cleanup.png) |

Original notes: [evidence/notes/original-notes.txt](evidence/notes/original-notes.txt)

---

## Remediation (Summary)

- **F-01**: Immediately upgrade or replace vsftpd 2.3.4. The backdoored version must never be used.
- Close or firewall unnecessary ports (especially 21, 23, 512–514, 1524, database ports).
- Disable anonymous FTP if not required.
- Replace Telnet and r-services with SSH.
- Keep all services patched and follow least-privilege principles.
- See [docs/remediation.md](docs/remediation.md) for detailed recommendations.

---

## Skills Demonstrated

- Network reconnaissance and host discovery
- Comprehensive port and service enumeration with Nmap
- Vulnerability identification (known backdoors)
- Exploitation using Metasploit Framework
- Post-exploitation (credential dumping, user enumeration)
- Temporary persistence and cleanup techniques
- Technical documentation and professional reporting
- Linux system interaction under root privileges

---

## Repository Structure

```
pentest-vm-lab/
├── README.md
├── docs/
│   ├── methodology.md
│   ├── architecture.md
│   ├── reconnaissance.md
│   ├── enumeration.md
│   ├── exploitation.md
│   ├── post-exploitation.md
│   ├── findings.md
│   ├── remediation.md
│   └── lessons-learned.md
├── evidence/
│   ├── screenshots/
│   └── notes/
├── commands/
│   └── commands-used.md
├── reports/
│   └── penetration-test-report.md
├── diagrams/
│   ├── network-architecture.png
│   └── attack-path.png
└── LICENSE
```

---

## Lessons Learned

See [docs/lessons-learned.md](docs/lessons-learned.md).

---

## Disclaimer

This project was performed **only** against an intentionally vulnerable virtual machine (Metasploitable 2) in an authorized, isolated lab environment under the tester’s full control.  

**Do not** use any techniques, commands, or modules documented here against systems you do not own or have explicit written permission to test. Unauthorized access to computer systems is illegal.

---

## License

This documentation is released under the MIT License. See [LICENSE](LICENSE).
