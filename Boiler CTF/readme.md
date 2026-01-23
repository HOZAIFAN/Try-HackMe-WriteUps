# 
```
██████╗  ██████╗ ██╗██╗     ███████╗██████╗      ██████╗████████╗███████╗
██╔══██╗██╔═══██╗██║██║     ██╔════╝██╔══██╗    ██╔════╝╚══██╔══╝██╔════╝
██████╔╝██║   ██║██║██║     █████╗  ██████╔╝    ██║        ██║   █████╗  
██╔══██╗██║   ██║██║██║     ██╔══╝  ██╔══██╗    ██║        ██║   ██╔══╝  
██████╔╝╚██████╔╝██║███████╗███████╗██║  ██║    ╚██████╗   ██║   ██║     
╚═════╝  ╚═════╝ ╚═╝╚══════╝╚══════╝╚═╝  ╚═╝     ╚═════╝   ╚═╝   ╚═╝     
```

<div align="center">

### 🔥 A TryHackMe Journey Through Web Exploitation & Privilege Escalation 🔥

**[Joomla CMS]** • **[sar2html RCE]** • **[SUID Exploitation]** • **[300 Points]**

---

*"Enumeration is not just a step—it's the foundation of every successful penetration test."*

</div>

---

## 🎯 THE MISSION

```
┌─────────────────────────────────────────────────────────┐
│  TARGET: Anonymous Production System                    │
│  OBJECTIVE: User Flag → Root Flag                       │
│  DIFFICULTY: ████████░░ Intermediate                    │
│  PATIENCE REQUIRED: ███████████ Maximum                 │
└─────────────────────────────────────────────────────────┘
```

This isn't your typical CTF. **Boiler** will test you in ways you don't expect. It trolls. It misdirects. It hides critical information in places you'd never think to look. 

**The Question:** Can you maintain your methodology when the system actively works against you?

---

## ⚡ WHAT AWAITS YOU

```
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  🚪 FOUR OPEN PORTS     →  Each tells a different story     │
│  🕵️  HIDDEN FILES        →  Nothing is as it seems          │
│  💀 INFINITE TROLLS      →  ROT13, dead ends, fake hints    │
│  🔓 CREDENTIAL LEAKS     →  In the most unexpected places   │
│  👑 SUID GOLDMINE        →  One misconfigured binary        │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 📖 THE STORY: FROM RECON TO ROOT

### 🔍 ACT I: THE RECONNAISSANCE

```
PORT      STATE    SERVICE       WHAT IT REVEALS
21/tcp    open     FTP          Anonymous access enabled
80/tcp    open     HTTP         Apache 2.4.18 (Joomla lurking)
10000/tcp open     Webmin       Red herring (waste your time here)
55007/tcp open     SSH          Non-standard port (THIS matters)
```

Four ports. Four possibilities. Most people rush to exploit Webmin on port 10000. They fail.

**The twist?** The real path requires you to enumerate deeper than you think necessary.

---

### 🌐 ACT II: WEB EXPLOITATION

```
http://target/
  ├── robots.txt ────────────────────→ ASCII troll (ignore)
  ├── /joomla/ ──────────────────────→ CMS installation
  │   ├── /administrator/ ───────────→ Locked down
  │   ├── /components/ ──────────────→ Standard files
  │   └── /_test/ ───────────────────→ 💎 JACKPOT
  │       └── sar2html ──────────────→ 🚨 VULNERABLE
  └── /manual/ ──────────────────────→ Apache docs (useless)
```

**sar2html** — a system monitoring tool that should never be public-facing. But here it is, with a **critical RCE vulnerability**.

One command injection. Full system access.

---

### 🔑 ACT III: CREDENTIAL HUNTING

SSH logs don't lie. When you gain RCE through sar2html, you can read system logs:

```log
Aug 20 11:16:35 parrot sshd[2451]: Accepted password for basterd 
from 10.1.1.1 port 49824 ssh2
#pass: superduperp@$$
```

**First foothold acquired.**

But that's not enough. You need lateral movement. Hidden in a backup script:

```bash
# /home/basterd/backup.sh
USER=stoner
#superduperp@$$no1knows
```

**Second user compromised.**

---

### 👑 ACT IV: THE CROWN

```
$ find / -perm /4000 -type f 2>/dev/null

/bin/find  ← 🚨 SUID BIT SET 🚨
```

When `find` runs with root privileges and you control the `-exec` flag:

```bash
find . -exec chmod 777 /root \;
```

**Game over. Root access achieved.**

---

## 🛠️ TECHNICAL BREAKDOWN

```
╔═══════════════════════════════════════════════════════════╗
║                    ATTACK CHAIN                           ║
╠═══════════════════════════════════════════════════════════╣
║                                                           ║
║  1. Nmap Scan          →  4 ports discovered             ║
║  2. FTP Anonymous      →  .info.txt (ROT13 troll)        ║
║  3. Directory Fuzzing  →  /joomla/_test/ found           ║
║  4. sar2html RCE       →  Command execution gained        ║
║  5. Read log.txt       →  basterd:superduperp@$$         ║
║  6. SSH Access         →  Initial foothold               ║
║  7. Find backup.sh     →  stoner:superduperp@$$no1knows  ║
║  8. Lateral Movement   →  User flag in .secret           ║
║  9. SUID Enumeration   →  find binary misconfigured      ║
║  10. Privilege Esc     →  ROOT ACCESS                    ║
║                                                           ║
╚═══════════════════════════════════════════════════════════╝
```

---

## 💡 LESSONS LEARNED

### 🎯 What This Room Teaches You

| Concept | Real-World Application |
|---------|----------------------|
| **Hidden Files** | Critical data in `.info.txt` and `.secret` — always use `ls -la` |
| **Non-Standard Ports** | SSH on 55007 instead of 22 — scan ALL ports |
| **Test Directories** | `/_test/` contained the vulnerable service — never ignore them |
| **Log Analysis** | SSH logs leaked plaintext credentials — read every log you can access |
| **Script Comments** | Developers hardcode passwords in comments — always check scripts |
| **SUID Binaries** | Misconfigured `find` = instant root — enumerate SUID files |

---

## 🧠 THE PSYCHOLOGY OF ENUMERATION

```
┌─────────────────────────────────────────────────────┐
│  TROLL #1: ROT13 encoded messages leading nowhere  │
│  TROLL #2: Empty directories (actually hidden files)│
│  TROLL #3: Up-to-date Webmin (waste of time)       │
│  TROLL #4: Sudo access denied (find another way)   │
└─────────────────────────────────────────────────────┘
```

**Every troll has a purpose:** To test if you'll give up or dig deeper.

Professional penetration testers don't quit when they hit dead ends. They adapt. They enumerate harder. They find another path.

**That's what this room teaches you.**

---

## 🔥 CRITICAL FINDINGS

```
╔════════════════════════════════════════════════════════╗
║  FINDING               LOCATION           IMPACT       ║
╠════════════════════════════════════════════════════════╣
║  Anonymous FTP         Port 21            Entry point  ║
║  sar2html RCE          /_test/            Code exec    ║
║  SSH Credentials       log.txt            User access  ║
║  Backup Script         backup.sh          Lateral move ║
║  User Flag             .secret            Objective 1  ║
║  SUID Binary           find command       Root access  ║
╚════════════════════════════════════════════════════════╝
```

---

## 🚀 EXPLOITATION PATH

```
    START
      │
      ▼
┌─────────────┐
│ Port Scan   │───→ Discovered: 21, 80, 10000, 55007
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ FTP Access  │───→ Found: .info.txt (troll)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Web Enum    │───→ Found: /joomla/_test/sar2html
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ RCE Exploit │───→ Command execution achieved
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Cred Hunt   │───→ log.txt reveals credentials
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ SSH Login   │───→ basterd user shell
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Lateral     │───→ backup.sh → stoner user
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ User Flag   │───→ .secret file
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ SUID Enum   │───→ find binary vulnerable
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ ROOT ACCESS │ 👑
└─────────────┘
```

---

## 🎓 THE METHODOLOGY THAT WINS

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃  1. SCAN ALL PORTS (not just common ones)         ┃
┃  2. CHECK EVERY SERVICE for anonymous access      ┃
┃  3. ALWAYS use ls -la (hidden files matter)       ┃
┃  4. ENUMERATE WEB DIRECTORIES thoroughly          ┃
┃  5. READ EVERY LOG FILE you can access            ┃
┃  6. CHECK SCRIPTS for hardcoded credentials       ┃
┃  7. TEST FOR COMMAND INJECTION on system tools    ┃
┃  8. ENUMERATE SUID BINARIES for privilege esc     ┃
┃  9. DON'T GIVE UP when you hit trolls             ┃
┃  10. DOCUMENT EVERYTHING as you go                ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

---

## 📊 ROOM STATISTICS

```
╔═══════════════════════════════════════╗
║  DIFFICULTY:      ████████░░  (8/10)  ║
║  POINTS EARNED:   300                 ║
║  TASKS COMPLETE:  2                   ║
║  FLAGS CAPTURED:  2                   ║
║  TROLLS FACED:    ∞                   ║
║  PATIENCE REQ:    Maximum             ║
╚═══════════════════════════════════════╝
```

---

## 🌟 WHY THIS MATTERS

**Boiler CTF isn't about exploiting one vulnerability.**

It's about:
- ✅ Chaining multiple small discoveries into complete compromise
- ✅ Maintaining methodology under frustration
- ✅ Reading between the lines (logs, scripts, hidden files)
- ✅ Adapting when obvious paths fail
- ✅ Building persistence through psychological warfare

**This mirrors real penetration testing** where:
- Services run on non-standard ports
- Credentials leak in unexpected places
- Misconfigurations create privilege escalation paths
- **Persistence and methodology matter more than tools**

---

## 🎭 THE FINAL LESSON

```
╔════════════════════════════════════════════════════════╗
║                                                        ║
║  "The difference between a script kiddie and a        ║
║   professional is not the tools they use, but the     ║
║   methodology they follow."                           ║
║                                                        ║
╚════════════════════════════════════════════════════════╝
```

When you get root access on Boiler, you won't just have flags. 

**You'll have a methodology that works on real systems.**

---

## 👤 ABOUT THIS WRITE-UP

```
┌────────────────────────────────────────────┐
│  Author: Muhammad Hozaifa Naeem           │
│  THM: @hyena11                             │
│  Date: January 23, 2026                    │
│  Room: Boiler CTF (TryHackMe)              │
└────────────────────────────────────────────┘
```

---

<div align="center">

## 🔥 READY TO TEST YOUR SKILLS? 🔥

**The system is waiting. The trolls are ready. The flags are hidden.**

### Are you methodical enough to find them?

---

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Boiler%20CTF-red?style=for-the-badge&logo=tryhackme)](https://tryhackme.com/)

---

⚠️ **DISCLAIMER:** This write-up is for educational purposes only. Always obtain proper authorization before testing any system's security.

</div>