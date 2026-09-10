# Findings

## F-01 – vsftpd 2.3.4 Backdoor

**Severity**  
Critical

**Description**  
The target was running vsftpd version 2.3.4. This specific version contains a malicious backdoor that was present in the official download archive for a short period in 2011. The backdoor can be triggered remotely without authentication, resulting in a root shell.

**Evidence**  
- Nmap version detection: `21/tcp open ftp vsftpd 2.3.4`  
- Successful exploitation via Metasploit module `exploit/unix/ftp/vsftpd_234_backdoor`  
- Screenshots: 02, 05, 06

**Technical Details**  
Nmap identified the exact version. The Metasploit module was used to trigger the backdoor by sending a crafted FTP username. A listening shell appeared on port 6200 and provided uid=0.

**Impact**  
Complete system compromise. An attacker obtains an interactive root shell and can read, modify, or destroy any data on the host, install persistence, or use the host as a pivot point.

**Attack Path**  
Reconnaissance → Service scan → Version identification → Metasploit exploitation → Root shell

**Remediation**  
- Immediately replace or upgrade vsftpd to a current, clean version.  
- Never use the 2.3.4 binary that was distributed during the backdoor window.  
- Verify package integrity (checksums/signatures) for all downloaded software.

**Validation**  
After remediation, re-scan with Nmap and confirm the version is no longer 2.3.4. Attempt the same Metasploit module; it should fail.

---

## F-02 – Excessive Open Ports & Outdated Services

**Severity**  
High

**Description**  
A large number of network services were exposed, many of them outdated and known to contain vulnerabilities (OpenSSH 4.7p1, Apache 2.2.8, Samba 3.0.20, MySQL 5.0.51a, PostgreSQL 8.3, etc.).

**Evidence**  
Full Nmap scan output (screenshots 02–04).

**Impact**  
Multiple independent attack vectors increase the likelihood of successful compromise even if one service is hardened.

**Remediation**  
- Disable or firewall every service that is not strictly required.  
- Apply vendor patches or replace end-of-life software.  
- Implement network segmentation and host-based firewalls.

---

## F-03 – Anonymous FTP Access

**Severity**  
Medium

**Description**  
The vsftpd service permitted anonymous login (Nmap script output: “Anonymous FTP login allowed”).

**Evidence**  
Nmap FTP scripts in screenshot 02.

**Impact**  
Allows unauthenticated users to list and potentially download or upload files, which can aid further attacks or data exfiltration.

**Remediation**  
Disable anonymous FTP unless there is a clear business need. Restrict write permissions and monitor usage.

---

## F-04 – Clear-Text & Insecure Remote Services

**Severity**  
High

**Description**  
Telnet (port 23) and classic r-services (ports 512–514) were enabled. These protocols transmit credentials and session data in clear text.

**Evidence**  
Nmap service list (screenshots 02–04).

**Impact**  
Credentials can be captured by any network observer. These services also frequently contain additional implementation flaws.

**Remediation**  
Disable Telnet and all r-services. Use SSH exclusively for remote administration.

---

## F-05 – Database Services Reachable from Network

**Severity**  
High

**Description**  
MySQL 5.0.51a (port 3306) and PostgreSQL 8.3 (port 5432) were listening and reachable from the attacker host.

**Evidence**  
Nmap output (screenshot 04).

**Impact**  
Outdated database engines often contain remote exploits or weak default configurations. Even without an immediate exploit, they expand the attack surface.

**Remediation**  
Bind database services to localhost or a management network only. Enforce strong authentication and keep database software patched.

---

## Summary Table

| ID   | Finding                          | Severity  |
|------|----------------------------------|-----------|
| F-01 | vsftpd 2.3.4 Backdoor            | Critical  |
| F-02 | Excessive open/outdated services | High      |
| F-03 | Anonymous FTP                    | Medium    |
| F-04 | Clear-text remote services       | High      |
| F-05 | Exposed database services        | High      |
