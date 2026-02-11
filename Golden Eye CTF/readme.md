# 🔱 GoldenEye — TryHackMe CTF Writeup

> *"For England, James..."*

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red?style=for-the-badge&logo=tryhackme)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner%20→%20Intermediate-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed%20✓-brightgreen?style=for-the-badge)
![Points](https://img.shields.io/badge/Points-420-gold?style=for-the-badge)
![Streak](https://img.shields.io/badge/Streak-27%20Days-blue?style=for-the-badge)

---

## 📋 Room Overview

| Field | Details |
|---|---|
| **Room Name** | GoldenEye |
| **Platform** | TryHackMe |
| **Author** | hyena11 |
| **Difficulty** | Beginner → Intermediate |
| **Theme** | James Bond / 007 |
| **Completed** | February 10, 2026 |
| **Tasks Completed** | 4 / 4 |
| **Points Earned** | 420 |

---

## 🎯 Objectives

- [x] Capture the **User Flag**
- [x] Capture the **Root Flag**
- [x] Achieve full system compromise

---

## ⚔️ Attack Chain Summary

```
[Nmap Scan] → [POP3 Enum: boris] → [POP3 Enum: natalya]
     ↓                ↓                      ↓
Open ports        username leak          training portal hint
25, 80, 55006,    natalya found          → web app pivot
55007 (POP3)

     ↓
[Moodle Web Portal] → [POP3 Enum: doak] → [Web Login: dr_doak]
     ↓                       ↓                    ↓
Dr. Doak message         email drops           higher-privilege
reveals "doak"         dr_doak creds         account access

     ↓
[Web Exploit] → [Reverse Shell] → [Kernel PrivEsc] → [ROOT 🏆]
```

---

## 🔍 Phase Breakdown

### Phase 01 — Reconnaissance & Enumeration

**Tool:** `nmap` via custom `flash.sh` script  
**Target:** `10.48.162.231`  
**Scan Type:** SYN Stealth Scan (NSE scripting enabled)

**Open Ports Discovered:**

| Port | Service | Notes |
|------|---------|-------|
| `25/tcp` | SMTP | Mail sending protocol |
| `80/tcp` | HTTP | Web server — Moodle portal |
| `55006/tcp` | Unknown | Non-standard — investigate further |
| `55007/tcp` | POP3 | **Primary attack vector** |

> 💡 The non-standard POP3 port (55007) is the key finding — standard port scans would miss this entirely.

---

### Phase 02 — POP3 Enumeration: User `boris`

**Tool:** `nc` (netcat)  
**Command:** `nc 10.48.162.231 55007`

```
USER boris
PASS secret1!
+OK Logged in.
```

**Emails Retrieved (3 messages):**

| # | From | Key Intel |
|---|------|-----------|
| 1 | `root@127.0.0.1` | Admin welcome — communication platform notice |
| 2 | `natalya@ubuntu` | *"Boris, I can break your codes!"* → **reveals username: natalya** |
| 3 | `alec@janus.boss` | Additional mission intelligence |

---

### Phase 03 — POP3 Enumeration: User `natalya`

**Command:** `nc 10.48.162.231 55007`

```
USER natalya
PASS bird
+OK Logged in.
```

**Emails Retrieved (2 messages):**

- Root warns Natalya to stop breaking Boris's codes
- Natalya identified as **GNO supervisor for training**
- ⚠️ Admin warns: *"GoldenEye is being sought after by a crime syndicate"*
- Hints toward a **web-based training portal** → leads to Phase 04

---

### Phase 04 — Web Application Discovery (Moodle)

**Target:** `http://10.48.162.231:80`  
**CMS:** Moodle Learning Management System  
**Logged in as:** Xenia X

Found an unread message from **Dr. Doak** in the internal messaging system:

> *"My email username is... **doak**"*

**Dr. Doak's Details:**
- Training Scientist — Sr Level Training Operating Supervisor
- GoldenEye Operations Center Sector 6
- Level 14 - NO2 - id:998623-1334

**New Target:** POP3 account `doak`

---

### Phase 05 — Credential Extraction: User `doak`

**Target IP:** `10.48.146.200`  
**Command:** `nc 10.48.146.200 55007`

```
USER doak
PASS goat
+OK Logged in.
```

**Critical Email Found (606 octets):**

```
James,

If you're reading this, congrats you've gotten this far.
You know how tradecraft works right?

Because I don't. Go to our training site and login to my
account....dig until you can exfiltrate further information.....

username: dr_doak
password: 4England!
```

> ⚠️ **CRITICAL:** Plaintext credentials stored in email — a severe security misconfiguration.

---

### Phase 06 + 07 — Exploitation & Privilege Escalation

1. Logged into Moodle as `dr_doak` with elevated privileges
2. Enumerated the web application for upload/injection vectors
3. Exploited web application vulnerability → **initial shell access**
4. Ran `uname -a` → identified vulnerable kernel version
5. Compiled and executed local privilege escalation exploit
6. Escalated to **root** → captured both flags

```
whoami
root
```

---

## 🏆 Completion

```
╔══════════════════════════════════════════════╗
║   Great work hyena11! Room completed! ✓      ║
║                                              ║
║   Completed Tasks  │  4                      ║
║   Points Earned    │  420                    ║
║   Streak           │  27 Days                ║
╚══════════════════════════════════════════════╝
```

---

## 🛡️ Security Recommendations

### Email Security
- **Enforce strong passwords** — `bird`, `goat`, `secret1!` are trivially guessable
- **Never store credentials in emails** — plaintext transmission is a critical failure
- **Use encrypted protocols** — POP3S instead of plain POP3
- **Enable multi-factor authentication** on all accounts

### Web Application Security
- **Input validation** — sanitize all user inputs to prevent injection
- **File upload restrictions** — validate file types, sizes, and content
- **Patch regularly** — keep all CMS components up to date
- **Secure session management** — prevent credential reuse across services

### System Security
- **Patch the kernel** — keep OS updated to prevent local privilege escalation
- **Principle of least privilege** — restrict user permissions to the minimum needed
- **Monitor and log access** — detect suspicious POP3 authentication attempts
- **Network segmentation** — isolate mail services from critical systems

---

## 🧠 Key Lessons Learned

1. **Always scan the full port range** — critical services hide on non-standard ports
2. **Email is a treasure trove** — POP3 mailboxes can contain credentials and usernames
3. **Follow every breadcrumb** — each phase led directly to the next through careful reading
4. **Credentials chain together** — one leaked password unlocked the entire system
5. **Methodical enumeration wins** — patience and thoroughness beat rushing every time

---

## 🗂️ Files in This Repository

| File | Description |
|------|-------------|
| `GoldenEye_CTF_Writeup.pdf` | Full detailed PDF writeup with screenshots |
| `README.md` | This file — quick-reference writeup summary |

---

## ⚙️ Tools Used

| Tool | Purpose |
|------|---------|
| `nmap` | Network port scanning & service detection |
| `nc` (netcat) | Manual POP3 interaction & mail retrieval |
| Browser | Moodle web application enumeration |
| Linux shell | Post-exploitation & privilege escalation |

---

## ⚠️ Disclaimer

> This writeup is created **exclusively for educational purposes**. All penetration testing techniques described herein should **only** be used in authorized environments — CTF challenges, engagements with explicit written authorization, or personal lab systems. Unauthorized access to computer systems is illegal under computer fraud and abuse laws worldwide.

---

<div align="center">

**Written by hyena11 · TryHackMe · February 10, 2026**

*"The name's hyena... hyena11."* 🔱

</div>
