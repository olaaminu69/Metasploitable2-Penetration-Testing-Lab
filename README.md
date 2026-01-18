# Metasploitable2 Penetration Testing Lab


Hands-on ethical hacking lab targeting the legendary **Metasploitable2** VM using **Kali Linux**.

This repository documents a complete reconnaissance and enumeration phase — the kind of workflow you’ll use daily in real penetration tests and OSCP-style exams.

## Target
- Metasploitable2 (Ubuntu 8.04-based intentionally vulnerable machine)
- IP: `192.168.x.x` (redacted)

## Tools Used
- Nmap
- enum4linux
- smbclient
- Nikto
- Kali Linux (2026 rolling)

## Key Findings (All Confirmed)

### Network & Service Discovery
- 23 open ports including FTP (anonymous), Telnet, SSH, Samba, HTTP, MySQL, PostgreSQL, VNC
- vsftpd 2.3.4 (famous backdoor)
- Samba 3.0.20-Debian (usermap_script RCE)
- Apache 2.2.8 + PHP 5.2.4 (EOL since ~2010–2017)

### SMB Vulnerabilities
- Null session allowed (`username: ''`, `password: ''`)
- Writable shares: `/tmp`, `/opt`
- NetBIOS name: **METASPLOITABLE**

### Web Application Vulnerabilities
- `phpinfo.php` exposed → full PHP & system info leak
- phpMyAdmin publicly accessible (default root/no password)
- TikiWiki installation (multiple RCE paths)
- Directory listings enabled (`/icons/`, `/doc/`)
- WebDAV (DAV/2) active
- HTTP TRACE method enabled (XST risk)
- Missing security headers

## Fastest Paths to Root (Lab Confirmed)
1. `exploit/multi/samba/usermap_script` → instant root
2. Anonymous SMB → upload webshell → reverse shell
3. phpMyAdmin → MySQL root → UDF privilege escalation
4. vsftpd 2.3.4 backdoor

## Screenshots
See `/screenshots/` folder for terminal proofs.

## Full Report
Detailed markdown report available at [`reports/Metasploitable2-Full-Report.md`](reports/Metasploitable2-Full-Report.md)

## Why This Lab Matters
Metasploitable2 teaches real-world lessons:
- The danger of anonymous services
- Why you must patch (or better, retire) ancient software
- How quickly weak configs lead to full compromise

Perfect practice for **OSCP, CEH, CompTIA PenTest+, Red Team roles**.

## Author
Aminu – Cybersecurity Enthusiast | Lagos, Nigeria  
GitHub: olaaminu69  
X/Twitter: [@amintemi69](https://twitter.com/amintemi69)

⭐ Star this repo if you're also grinding labs in 2026!

#CyberSecurity #EthicalHacking #PenetrationTesting #OSCP #Metasploitable2 #KaliLinux #RedTeam
