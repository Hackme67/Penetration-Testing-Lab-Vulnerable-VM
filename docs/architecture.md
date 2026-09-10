# Lab Architecture

## Network Topology

The assessment was conducted on an isolated private network:

```
10.10.1.0/24
├── 10.10.1.2   (live host – not targeted)
├── 10.10.1.3   Attacker (Parrot OS – zen@parrot)
└── 10.10.1.4   Target (Metasploitable 2)
```

![Network Architecture Diagram](../diagrams/network-architecture.png)

## Attacker Machine

| Attribute     | Value                |
|---------------|----------------------|
| Hostname      | parrot               |
| User          | zen                  |
| Operating System | Parrot OS         |
| IP Address    | 10.10.1.3            |
| Interface     | enp0s3               |
| MAC           | 08:00:27:54:c1:71    |

Confirmed via `ifconfig` output in the evidence.

## Target Machine

| Attribute     | Value                          |
|---------------|--------------------------------|
| Role          | Intentionally vulnerable VM    |
| Distribution  | Metasploitable 2 (Ubuntu-based)|
| IP Address    | 10.10.1.4                      |
| Hostname (from services) | metasploitable.localdomain |
| MAC Address   | 08:00:27:B7:A1:E7 (VirtualBox) |

## Exposed Attack Surface (High-Level)

The target presented a large attack surface typical of Metasploitable 2, including:

- FTP (vsftpd 2.3.4 – primary exploitation vector)
- SSH, Telnet, SMTP, DNS, HTTP, RPC, Samba, NFS
- Database services (MySQL, PostgreSQL)
- VNC, IRC, Tomcat, Java RMI, and others

Full service list is documented in [enumeration.md](enumeration.md).

## Isolation Notes

- All testing occurred inside a controlled lab network.
- No production systems or external networks were involved.
- The target is designed to be vulnerable for educational purposes.
