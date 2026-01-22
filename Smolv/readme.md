# 🔐 Smolv - TryHackMe Room Write-Up

> *"When WordPress becomes your worst nightmare"*

A comprehensive walkthrough of **Smolv**, a medium-difficulty WordPress exploitation challenge that'll teach you the art of turning database access into root shells.

## 🎭 The Challenge

This isn't your average WordPress box. Smolv takes you on a journey from vulnerable plugin exploitation to database credential harvesting, hash cracking, and finally privilege escalation. Along the way, you'll learn the critical difference between MySQL prompts and Linux shells, master the art of shell stabilization, and discover why password reuse is a pentester's best friend.

## 🎯 The Hunt

**From Web App to Root:**

What starts as WordPress reconnaissance evolves into a multi-stage attack involving LFI exploitation through the jsmol2wp plugin, WordPress configuration file extraction, database enumeration, password hash cracking with John the Ripper (using the correct phpass format!), and systematic lateral movement to claim both flags.

**The Highlights:**
- 🔍 Discover the vulnerable jsmol2wp plugin through clever enumeration
- 🗄️ Extract wp-config.php via LFI to harvest database credentials
- 🔧 Master the 5-step shell stabilization process for a fully interactive TTY
- 🔨 Crack WordPress password hashes (hint: it's phpass, not "wordpress"!)
- 👤 Navigate the maze of database users vs system users

## 🚩 Captured Flags

**User Flag:** `45edaec653df9ee06236b72686963`  
**Root Flag:** *[Complete the privilege escalation to find out!]*

## 💡 Critical Lessons

**The Big Mistakes Everyone Makes:**
- ❌ Trying to run `su` commands inside a MySQL prompt (spoiler: it won't work!)
- ❌ Using `--format=wordpress` with John (the correct format is `phpass`)
- ❌ Forgetting the full shell stabilization process (PTY spawn → Ctrl+Z → stty → fg → export)
- ❌ Assuming database passwords work for SSH (different authentication systems!)
- ❌ Thinking database users are Linux users (wpuser won't work with `su`)

**What Actually Works:**
- ✅ Understanding environment contexts (MySQL vs Linux shell)
- ✅ Testing password reuse systematically (diego's WordPress password = Linux password!)
- ✅ Proper hash format identification before cracking
- ✅ Complete TTY upgrade for stable shell operations

## 🛠️ The Arsenal

**Tools You'll Master:**
- WPScan for WordPress vulnerability scanning
- Netcat for reverse shell catching
- MySQL for database enumeration
- John the Ripper (with the correct phpass format!)
- Python for PTY shell spawning
- stty for terminal configuration

## ⭐ Why Read This?

**Perfect for:** Intermediate hackers who want to understand web application exploitation, database enumeration, and the subtle differences between various authentication systems. This writeup doesn't just show you what to do—it explains *why* things fail and how to fix them.

**Key Takeaways:**
- WordPress plugin vulnerabilities and LFI exploitation
- Database vs system authentication boundaries
- The art of password reuse testing
- Shell stabilization techniques that actually work
- Real-world lateral movement scenarios

## 🎓 Verdict

**Difficulty:** Medium | **Learning Value:** High

A perfect blend of web exploitation and system compromise that teaches critical pentesting fundamentals. The detailed explanations of common mistakes make this an invaluable resource for anyone learning WordPress security.

---

**Completed:** January 22, 2026  
*Educational purposes only. Hack responsibly with proper authorization.*