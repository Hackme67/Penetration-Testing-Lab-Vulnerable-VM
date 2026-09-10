# Enumeration

## Objective

Identify open ports, running services, and software versions on the target (10.10.1.4) to locate potential vulnerabilities.

## Full Port & Service Scan

**Tool**  
Nmap

**Command**
```bash
sudo nmap 10.10.1.4 -Pn -sV -A -T4 -p0-65535
```

**Flags explained**  
- `-Pn` – Treat host as online (skip host discovery)  
- `-sV` – Service/version detection  
- `-A` – Enable OS detection, version detection, script scanning, and traceroute  
- `-T4` – Aggressive timing  
- `-p0-65535` – Scan all TCP ports  

**Evidence**  
- [02-nmap-service-scan-part1.png](../evidence/screenshots/02-nmap-service-scan-part1.png)  
- [03-nmap-service-scan-part2.png](../evidence/screenshots/03-nmap-service-scan-part2.png)  
- [04-nmap-service-scan-part3.png](../evidence/screenshots/04-nmap-service-scan-part3.png)

---

## Discovered Services

| Port    | Protocol | Service          | Version / Notes                                      | Security Significance                          |
|---------|----------|------------------|------------------------------------------------------|------------------------------------------------|
| 21/tcp  | TCP      | ftp              | vsftpd 2.3.4                                         | **Critical** – known backdoor                  |
| 22/tcp  | TCP      | ssh              | OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)         | Outdated; potential weak ciphers               |
| 23/tcp  | TCP      | telnet           | Linux telnetd                                        | Clear-text authentication                      |
| 25/tcp  | TCP      | smtp             | Postfix smtpd (metasploitable.localdomain)         | Information disclosure, potential relay issues |
| 53/tcp  | TCP      | domain           | ISC BIND 9.4.2                                       | Outdated DNS server                            |
| 80/tcp  | TCP      | http             | Apache httpd 2.2.8 ((Ubuntu) DAV/2) – title “HACKER” | Outdated web server; TRACE method enabled      |
| 111/tcp | TCP      | rpcbind          | 2 (RPC #100000)                                      | RPC services exposed                           |
| 139/tcp | TCP      | netbios-ssn      | Samba smbd 3.X – 4.X (workgroup: WORKGROUP)          | File sharing; known Samba issues               |
| 445/tcp | TCP      | netbios-ssn      | Samba smbd 3.0.20-Debian                             | Same as above                                  |
| 512/tcp | TCP      | exec             | netkit-rsh rexecd                                    | Insecure r-services                            |
| 513/tcp | TCP      | login?           | –                                                    | Insecure r-services                            |
| 514/tcp | TCP      | tcpwrapped       | –                                                    | Related to r-services                          |
| 1099/tcp| TCP      | java-rmi         | GNU Classpath grmiregistry                           | Java RMI registry                              |
| 1524/tcp| TCP      | bindshell        | Metasploitable root shell                            | Direct root shell (intentional)                |
| 2049/tcp| TCP      | nfs              | 2-4 (RPC #100003)                                    | Network file system                            |
| 2121/tcp| TCP      | ftp              | ProFTPD 1.3.1                                        | Secondary FTP service                          |
| 3306/tcp| TCP      | mysql            | MySQL 5.0.51a-3ubuntu5                               | Outdated database; remote access               |
| 3632/tcp| TCP      | distccd          | distccd v1 ((GNU) 4.2.4 …)                           | Distributed compiler daemon                    |
| 5432/tcp| TCP      | postgresql       | PostgreSQL DB 8.3.0 – 8.3.7                          | Outdated database                              |
| 5900/tcp| TCP      | vnc              | VNC (protocol 3.3)                                   | Remote desktop; authentication required        |
| 6000/tcp| TCP      | X11              | (access denied)                                      | X Window System                                |
| 6667/tcp| TCP      | irc              | UnrealIRCd                                           | IRC server                                     |
| 6697/tcp| TCP      | irc              | UnrealIRCd                                           | IRC server                                     |
| 8009/tcp| TCP      | ajp13            | Apache Jserv (Protocol v1.3)                         | Tomcat AJP connector                           |
| 8180/tcp| TCP      | http             | Apache Tomcat/Coyote JSP engine 1.1                  | Tomcat management interface                    |

Additional RPC-related ports (mountd, nlockmgr, status, etc.) were also observed.

---

## Key Observations

1. **vsftpd 2.3.4** on port 21 is the primary high-value finding. This exact version contains a malicious backdoor.
2. Anonymous FTP login is allowed (confirmed by Nmap scripts).
3. Multiple legacy clear-text and r-services (Telnet, rexec, rlogin) remain enabled.
4. Database ports (MySQL 3306, PostgreSQL 5432) are reachable from the attacker network.
5. Port 1524 offers a direct “Metasploitable root shell” – an intentional feature of the lab VM.
6. The HTTP service on port 80 returns the title “HACKER”.

These findings guided the decision to prioritize the vsftpd 2.3.4 backdoor for exploitation.
