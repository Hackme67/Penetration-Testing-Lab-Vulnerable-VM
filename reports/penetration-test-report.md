# Penetration Test Report  
## Metasploitable 2 – Authorized Lab Assessment

**Date of Testing:** 10 September 2026  
**Tester:** zen (Parrot OS lab environment)  
**Classification:** Lab Exercise – Authorized Only  

---

## 1. Executive Summary

A penetration test was performed against an intentionally vulnerable virtual machine (Metasploitable 2) located at IP address 10.10.1.4 on an isolated laboratory network. The objective was to identify vulnerabilities, demonstrate realistic exploitation, and document remediation guidance.

The most critical finding was the presence of vsftpd version 2.3.4, which contains a well-known backdoor. Using the Metasploit Framework, this vulnerability was exploited to obtain an immediate root-level command shell. Subsequent post-exploitation activities confirmed the ability to read sensitive system files (including password hashes) and to create persistent local accounts.

Because the backdoor granted root privileges directly, no additional privilege-escalation steps were required. The engagement successfully demonstrated a complete compromise path from initial network discovery to full administrative control.

All testing was conducted solely within an authorized lab environment under the tester’s control. No production systems were involved.

**Overall Risk Rating for the Target Host:** Critical

---

## 2. Scope

| Item                    | Details                                      |
|-------------------------|----------------------------------------------|
| Target                  | Single host – 10.10.1.4 (Metasploitable 2)   |
| Network                 | 10.10.1.0/24 (isolated lab)                  |
| Attacker                | 10.10.1.3 (Parrot OS)                        |
| In-scope activities     | Reconnaissance, scanning, exploitation, post-exploitation, documentation |
| Out-of-scope            | Other live hosts, web application deep testing, social engineering, physical access, denial-of-service |

---

## 3. Rules of Engagement / Lab Authorization

- Testing was limited to systems explicitly owned and controlled by the tester.
- The target is an intentionally vulnerable distribution designed for security training.
- No attempts were made to access systems outside the designated lab network.
- All activities complied with applicable laws and institutional policies governing authorized security testing.

---

## 4. Target Information

| Attribute          | Value                                      |
|--------------------|--------------------------------------------|
| IP Address         | 10.10.1.4                                  |
| Operating System   | Ubuntu-based (Metasploitable 2)            |
| Hostname (observed)| metasploitable.localdomain                 |
| MAC Address        | 08:00:27:B7:A1:E7 (VirtualBox virtual NIC) |
| Primary Vector     | vsftpd 2.3.4 on TCP/21                     |

---

## 5. Methodology

The assessment followed a standard penetration-testing workflow:

1. Host discovery  
2. Full port and service enumeration  
3. Vulnerability identification  
4. Exploitation  
5. Post-exploitation and evidence collection  
6. Cleanup and reporting  

Detailed methodology is documented in `docs/methodology.md`.

---

## 6. Attack Surface

Nmap identified a large number of listening services. The most relevant for this engagement were:

- TCP/21 – vsftpd 2.3.4 (backdoored)
- TCP/22 – OpenSSH 4.7p1
- TCP/23 – Telnet
- TCP/80 – Apache 2.2.8
- TCP/139/445 – Samba 3.0.20
- TCP/3306 – MySQL 5.0.51a
- TCP/5432 – PostgreSQL 8.3
- Multiple additional RPC, NFS, IRC, VNC, and Tomcat services

A complete service table is available in `docs/enumeration.md`.

---

## 7. Findings Summary

| ID   | Finding                          | Severity  | Status     |
|------|----------------------------------|-----------|------------|
| F-01 | vsftpd 2.3.4 Backdoor            | Critical  | Exploited  |
| F-02 | Excessive outdated services      | High      | Observed   |
| F-03 | Anonymous FTP enabled            | Medium    | Observed   |
| F-04 | Clear-text remote services       | High      | Observed   |
| F-05 | Database ports exposed           | High      | Observed   |

---

## 8. Detailed Findings

See `docs/findings.md` for full technical descriptions, evidence references, impact analysis, and remediation steps for each finding.

**Primary exploited vulnerability (F-01):**  
The vsftpd 2.3.4 backdoor was triggered via the Metasploit module `exploit/unix/ftp/vsftpd_234_backdoor`. A root shell (uid=0) was obtained within seconds of launching the exploit.

---

## 9. Attack Path

```
Host Discovery (nmap -sn)
        ↓
Full Service Scan (nmap -sV -A -p0-65535)
        ↓
Identification of vsftpd 2.3.4
        ↓
Metasploit Exploitation
        ↓
Root Command Shell
        ↓
Credential Dumping & Persistence Demonstration
        ↓
Cleanup
```

Visual diagram: `diagrams/attack-path.png`

---

## 10. Impact Assessment

Successful exploitation of the vsftpd backdoor resulted in:

- Complete administrative control of the host
- Ability to read any file, including `/etc/shadow`
- Ability to create and delete local user accounts
- Potential for further lateral movement or data destruction (not exercised)

In a real-world scenario, this level of access would allow an attacker to steal data, install ransomware, or use the compromised host as a beachhead into a larger network.

---

## 11. Remediation Recommendations

Priority actions:

1. **Immediate** – Replace vsftpd 2.3.4 with a clean, current version and verify package integrity.
2. **High** – Disable or firewall all unnecessary services (especially Telnet, r-services, and remote database listeners).
3. **High** – Implement host-based firewall rules restricting inbound traffic to only required ports.
4. **Medium** – Disable anonymous FTP if not required.
5. **Ongoing** – Establish a regular patching and vulnerability-scanning cadence.

Detailed recommendations are provided in `docs/remediation.md`.

---

## 12. Testing Limitations

- Only one host was actively targeted.
- Alternative exploitation paths (Samba, distcc, UnrealIRCd, etc.) were not pursued after root access was obtained via vsftpd.
- Password-hash cracking was noted as a possible next step but was not performed.
- No web-application-specific testing (directory brute-forcing, parameter fuzzing) was conducted.
- Network pivoting to other hosts was out of scope.

---

## 13. Conclusion

The assessment demonstrated that a single, well-known vulnerability in an outdated service was sufficient to achieve complete compromise of the target system. The engagement successfully exercised the full penetration-testing lifecycle—from reconnaissance through exploitation, post-exploitation, and professional documentation.

The results underscore the critical importance of timely patching, minimal attack surface, and continuous monitoring. All findings and remediation guidance are intended solely for educational and defensive improvement purposes within authorized environments.

---

**End of Report**
