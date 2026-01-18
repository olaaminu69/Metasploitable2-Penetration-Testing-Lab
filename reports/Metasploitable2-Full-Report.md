# Metasploitable2 – Full Penetration Testing Report

**Author:** olaaminu69  
**Location:** Lagos, Nigeria  
**Date of Testing:** January 13–18, 2026  
**Environment:** Kali Linux (rolling release) – Isolated lab VM  
**Target:** Metasploitable2 (classic intentionally vulnerable machine)  
**Purpose:** Educational / Ethical Hacking Practice (OSCP-style workflow)

**⚠️ IMPORTANT DISCLAIMER**  
All activities were performed in a controlled, isolated virtual lab environment with explicit permission. This report is for learning and portfolio purposes only. Never perform these actions on systems you do not own or have written authorization for.

## Executive Summary

Metasploitable2 is a deliberately vulnerable Ubuntu-based VM designed for penetration testing training. This lab exercise demonstrated a full reconnaissance → enumeration → vulnerability identification workflow.

**Key Critical Findings:**
- 20+ open ports with legacy/insecure services (FTP anonymous, Telnet, SMB null session, outdated Apache/PHP, etc.)
- **Anonymous/null session** access via Samba → writable shares (`/opt`, `/tmp`)
- Massive information disclosure via `phpinfo.php`, phpMyAdmin (unprotected), directory listings
- Multiple instant root paths: Samba usermap_script (CVE-2007-2447), vsftpd 2.3.4 backdoor, phpMyAdmin → UDF escalation
- Extremely outdated software (Apache 2.2.8, PHP 5.2.4, Samba 3.0.20 – all EOL 10+ years ago)

**Overall Risk Level:** **CRITICAL** – This machine can be fully compromised in under 5 minutes using public exploits.

## 1. Reconnaissance & Service Discovery

**Tool:** Nmap 7.95 (`-sC`, `-sV`, `--script=http-enum`)

**Major Discoveries:**
- Host up with ~23 open TCP ports
- Signature services: vsftpd 2.3.4 (backdoor), Samba 3.0.20, Apache 2.2.8 (Ubuntu) DAV/2
- Weak SSL/TLS (SSLv2 supported, broken ciphers, expired 2010 cert)
- HTTP title: "Metasploitable2 - Linux"

Here are some classic Nmap outputs from Metasploitable2 scans:



## 2. SMB Enumeration & Anonymous Access

**Tools:** enum4linux v0.9.1 + smbclient

**Critical Findings:**
- Null session allowed: `username: ''` / `password: ''`
- Workgroup: WORKGROUP
- NetBIOS name: **METASPLOITABLE**
- Shares exposed & writable: `print$`, `tmp`, `opt`, `IPC$`
- Samba version: **3.0.20-Debian** → vulnerable to `usermap_script` RCE (CVE-2007-2447)

Classic anonymous smbclient output:



## 3. Web Server & Application Enumeration

**Tools:** Nmap http-enum script + Nikto v2.5.0

**Major Exposures:**
- Apache/2.2.8 (Ubuntu) DAV/2 + PHP/5.2.4-2ubuntu5.10
- Exposed files/directories:
  - `/phpinfo.php` → full system & PHP config leak
  - `/phpMyAdmin/` → likely default root/no password
  - `/tikiwiki/`, `/test/`, `/doc/`, `/icons/` (directory listings enabled)
- Missing headers: X-Frame-Options, X-Content-Type-Options
- TRACE method active → possible XST
- WebDAV enabled → potential unauthenticated PUT uploads

**Nikto Scan Highlights:**



**phpinfo.php Exposure (massive info disclosure):**



**phpMyAdmin – One of the fastest compromise paths:**



## 4. Fastest Exploitation Paths (Lab-Verified Concepts)

| Rank | Vector                                      | CVE / Technique                          | Expected Result             | Difficulty |
|------|---------------------------------------------|------------------------------------------|-----------------------------|------------|
| 1    | Samba usermap_script                        | CVE-2007-2447                            | Instant root shell          | Trivial    |
| 2    | vsftpd 2.3.4 backdoor                       | Backdoor (port 21 smiley face)           | Root shell                  | Easy       |
| 3    | Anonymous SMB → upload PHP webshell → www-data | Writable /opt or /tmp                   | Reverse shell → priv esc    | Easy       |
| 4    | phpMyAdmin → MySQL root → UDF code exec     | Default creds + user-defined functions   | System root                 | Easy       |
| 5    | TikiWiki / DVWA / other web apps            | Multiple known RCEs                      | www-data / root             | Medium     |

Metasploitable2 home page and vulnerable web apps (examples):



## 5. Lessons Learned & Hardening Recommendations

- **Disable unnecessary services** (Telnet, anonymous FTP, null SMB sessions)
- **Patch & upgrade** – Anything pre-2015 is likely broken
- **Enforce authentication** – No guest/anonymous access ever
- **Remove test pages** (phpinfo, phpMyAdmin, default dirs)
- **Firewall everything** – Only expose what is needed
- **Use modern protocols** (TLS 1.3+, strong ciphers, security headers)

## Conclusion

Metasploitable2 is the perfect training target to understand how quickly poor configuration leads to total compromise.

This lab reinforced core pentesting principles:
- Recon is 80% of the work
- Information disclosure is often the first domino
- Legacy software + default creds = game over

Happy (and always ethical) hacking! 🦄💀

**Aminu** – Cybersecurity Enthusiast | Lagos, NG  
GitHub: olaaminu69/Metasploitable2-Penetration-Testing-Lab  
X: [@amintemi69](https://x.com/amintemi69)

Last updated: January 18, 2026
