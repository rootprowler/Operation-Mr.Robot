# Personal Notes - Mr. Robot Pentest Project

## Overview

This document captures all relevant notes, thoughts, and observations made during the **Mr. Robot Pentest Project**, simulating a black-box external penetration test. The goal was to exploit vulnerabilities in the target system (WordPress-based site running on Apache) and escalate privileges to gain full system control. These notes are part of my methodology and learning process, helping me improve my offensive security skills.

---

## Objectives

1. **External Penetration Test**: Conduct a simulated attack to compromise the web server.
2. **Privilege Escalation**: From initial access (www-data) to privileged access (root).
3. **Capture Flags**: Collect flags placed on the system as part of the challenge.

---

## Network Setup

- **Attacker**: Kali Linux (192.168.254.130)
- **Target**: Mr. Robot (192.168.254.133)
- **Network Mode**: Bridged mode to ensure machines are in the same network.

---

## Enumeration Phase

The enumeration phase is crucial to understanding the target system. The following tools and techniques were used to gather information.

### Nmap Scan

A thorough Nmap scan was conducted to identify open ports, services, and vulnerabilities:

```bash
nmap -sS -A -p- 192.168.254.133
```

**Results:**

- Open port: 80 (Apache HTTPD)
- WordPress site identified at the root (/)

### Web Directory Enumeration (Gobuster)

Gobuster was used to identify hidden files or directories that may provide further attack vectors.

```bash
gobuster dir -u http://192.168.254.133 -w /usr/share/wordlists/dirb/common.txt
```

**Results:**

- Discovered /wp-login.php, /license.txt, /robots.txt

**Insight:** WordPress login page accessible, which is a potential target for brute force.

---

## Exploitation Phase

### WordPress Login Brute Force Attack (Hydra)

Since the WordPress login page was discovered during enumeration, a brute force attack was conducted using Hydra with a common password list (rockyou.txt).

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.254.133 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In:S=Dashboard"
```

**Outcome:**

- Cracked credentials: `admin:welcome`
- Logged into the WordPress Dashboard, gaining administrator access.

### Web Shell Upload

Once inside WordPress, I exploited the 404.php template of the Twenty Fifteen theme to upload a PHP reverse shell.

- Modified `404.php` to include reverse shell code from PentestMonkey's PHP Reverse Shell.
- Listener was set up on Kali Linux:

```bash
nc -lvnp 4444
```

- Triggered the shell by navigating to:

```bash
http://192.168.254.133/wp-content/themes/twentyfifteen/404.php
```

**Result:**

- Successfully received a reverse shell with user `www-data`.

---

## Privilege Escalation Phase

### From www-data to robot

While exploring the filesystem, I found a file called `password.raw-md5` in the `/home/robot` directory. This file appeared to contain an MD5 hash that might represent the user's password.

- Cracked the MD5 hash using an online tool like CrackStation.
- The password for `robot` was cracked: `robot1234`
- Logged in as `robot` using the cracked password.

### From robot to root

After gaining access as `robot`, I searched for SUID binaries, which could potentially allow privilege escalation. Used the following command:

```bash
find / -perm -4000 2>/dev/null
```

- Discovered the `nmap` binary with the SUID bit set: `/usr/local/bin/nmap`
- Ran `nmap` with the `--interactive` flag to spawn a shell with root privileges:

```bash
nmap --interactive
!sh
```

**Result:**

- Gained full root access to the system.

---

## Flag Capture

Once root access was achieved, I captured all three flags that were part of the challenge.

- `flag1.txt`: `073403c8a58a1f80d943455fb30724b9`
- `flag2.txt`: `822c73956184f694993bede3eb39f959`
- `root-flag.txt`: `04787ddef27c3dee1ee161b21670b4e4`

---

## Post-Exploitation & Clean-Up

Once the flags were captured, I ensured that all traces of my actions were wiped from the system to avoid leaving any backdoors or logs. This step is vital in a real-world scenario to prevent detection.

---

## Lessons Learned

- **Reconnaissance**: Thorough enumeration is critical. Identifying misconfigurations and weak points early on can accelerate exploitation.
- **WordPress Weaknesses**: WordPress sites with weak or default credentials are an easy target for brute force attacks. Regular password hygiene and account lockout policies are necessary to mitigate this.
- **Privilege Escalation**: Always check for SUID binaries and other potential escalation vectors after gaining initial access.
- **Web Shells**: Modifying web templates, such as `404.php`, is an effective way to upload shells when the site is misconfigured.

---

## Areas for Improvement

- **Faster Brute Force**: In the future, I will consider using more optimized wordlists or tools like Burp Suite to speed up the process.
- **Detection Evasion**: I will practice more advanced evasion techniques such as time-based shell creation, avoiding obvious file names, and clearing logs more effectively.

---

## Conclusion

This pentest was an excellent opportunity to practice full-chain attack techniques—from web exploitation to internal privilege escalation. The target system had multiple vulnerabilities, including weak credentials and misconfigured WordPress themes, which were leveraged to gain full root access. Moving forward, I plan to document more specific countermeasures for each vulnerability and integrate them into my overall security improvement strategies.

