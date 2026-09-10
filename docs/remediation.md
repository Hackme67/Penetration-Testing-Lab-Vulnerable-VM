# Remediation Recommendations

Recommendations are based solely on the findings observed during this assessment.

## Critical Priority

### F-01 – vsftpd 2.3.4 Backdoor

- Remove the backdoored vsftpd 2.3.4 binary immediately.
- Install a current, verified version of vsftpd (or an alternative FTP server) from a trusted package repository.
- Verify package signatures/checksums before installation.
- After replacement, confirm with Nmap that the version string is no longer 2.3.4 and that the Metasploit module no longer succeeds.

## High Priority

### Reduce Attack Surface (F-02, F-04, F-05)

- Close or firewall all ports that are not required for the system’s intended function.
- Disable Telnet, rexec, rlogin, and any other clear-text remote access services.
- Restrict MySQL and PostgreSQL to listen only on localhost or a dedicated management network.
- Replace end-of-life software (Apache 2.2.8, Samba 3.0.20, OpenSSH 4.7p1, etc.) with supported versions.

### Network Controls

- Implement a host-based firewall (iptables/nftables or equivalent) that permits only necessary inbound traffic.
- Place the host in a segmented network zone with strict inter-zone rules.

## Medium Priority

### F-03 – Anonymous FTP

- Disable anonymous access unless there is a documented business requirement.
- If anonymous access must remain, make the share read-only and monitor for abuse.

## General Hardening Guidance

- Apply the principle of least privilege to all services and user accounts.
- Keep the operating system and all installed packages patched.
- Monitor authentication logs and unusual process activity.
- Regularly re-scan the host after changes to validate that remediation has been effective.

## Validation Steps

1. Re-run a full Nmap service scan and compare results with the original scan.
2. Attempt the same Metasploit module used in the assessment; it should fail.
3. Confirm that previously open high-risk ports are now closed or filtered.
4. Verify that database ports are no longer reachable from untrusted networks.
