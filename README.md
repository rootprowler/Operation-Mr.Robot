# 🛡️ Operation: Mr. Robot Infiltration

**RootProwler Advanced Pentest | VulnHub | Full Chain: Web → Internal → Root**

---

## 📍 Target Info

- **VM**: Mr. Robot (VulnHub)
- **Attacker**: Kali Linux
- **Difficulty**: Intermediate
- **Network Setup**: Host-only (192.168.254.x range)

---

## 🧭 Attack Flow

1. 🔍 Recon & Port Scan
2. 🌐 Web Enumeration
3. 💉 WordPress Exploitation
4. 🐚 Reverse Shell Access
5. 🧑‍💻 Privilege Escalation (robot → root)
6. 🗝️ Flag Collection

---

## 🛠️ Tools Used

- `nmap`, `gobuster`, `wpscan`, `hydra`, `netcat`, `GTFOBins`
- Manual enumeration + reverse shell via `404.php`

---

## 🧵 Step-by-Step Summary

### 1. 🔍 Recon
```bash
nmap -A -p- 192.168.254.133
# Found port 80 and 443
