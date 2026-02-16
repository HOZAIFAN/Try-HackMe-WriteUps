# 🔥 CTF Write-Ups Collection

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Active-red)](https://tryhackme.com/p/hyena11)
[![CTF](https://img.shields.io/badge/CTF-LAFB%202026-brightgreen)](https://tryhackme.com)
[![Streak](https://img.shields.io/badge/Streak-33%20Days-orange)](https://tryhackme.com/p/hyena11)
[![Write-Ups](https://img.shields.io/badge/Write--Ups-1-blue)](.)

> **Author:** hyena11  
> **Platform:** TryHackMe  
> **Event:** Love at First Breach (LAFB) CTF 2026

---

## 📚 About This Repository

This repository contains detailed write-ups for Capture The Flag (CTF) challenges completed on TryHackMe during the **Love at First Breach 2026** event. Each write-up includes comprehensive documentation of the methodology, tools, techniques, and thought processes used to solve the challenges.

### 🎯 Purpose

- **Educational Resource:** Help others learn cybersecurity concepts through practical examples
- **Knowledge Sharing:** Document solutions and techniques for the community
- **Personal Reference:** Maintain a portfolio of completed challenges and skills developed
- **Methodology Documentation:** Showcase systematic approaches to security challenges

---

## 🏆 CTF Event Information

| Event Details | |
|---------------|---|
| **CTF Name** | Love at First Breach 2026 (LAFB CTF) |
| **Platform** | TryHackMe |
| **Year** | 2026 |
| **Player** | hyena11 |
| **Current Streak** | 33 Days |
| **Status** | Active |

---

## 📁 Write-Ups Included

### 1. 💘 Hidden Deep Into My Heart

**Category:** Web Exploitation  
**Difficulty:** Beginner  
**Points:** 100  
**Status:** ✅ Completed

A beginner-friendly web exploitation challenge focusing on enumeration techniques, directory discovery through `robots.txt` analysis, and credential guessing using contextual clues.

**Key Concepts:**
- robots.txt analysis
- Directory enumeration with Gobuster
- Credential guessing techniques
- Hidden endpoint discovery

**Flag:** `THM{lov3_is_in_th3_robots_txt}`

**[📄 Full Write-Up](./Hidden_Deep_Into_My_Heart_Writeup.md)**

---

## 🛠️ Tools & Technologies Used

### Enumeration Tools
- **Gobuster 3.6** - Directory and file bruteforcing
- **Web Browser** - Manual navigation and observation
- **cURL** - HTTP request testing (optional)

### Analysis Tools
- **Burp Suite** - Request interception (optional)
- **Text Editor** - Log analysis and note-taking

### Target Technologies
- **Python/Flask** - Web application framework
- **HTTP/HTTPS** - Web protocols

---

## 🎓 Skills Demonstrated

### Web Security
- ✅ Web application enumeration
- ✅ robots.txt analysis and exploitation
- ✅ Directory traversal and discovery
- ✅ Authentication bypass techniques
- ✅ Hidden endpoint identification

### Reconnaissance
- ✅ Information gathering from public resources
- ✅ Directory bruteforcing
- ✅ Pattern recognition in CTF challenges

### Critical Thinking
- ✅ Logical deduction from hints
- ✅ Contextual credential guessing
- ✅ Systematic methodology application

---

## 📊 Challenge Statistics

| Metric | Value |
|--------|-------|
| **Total Challenges Completed** | 1 |
| **Total Points Earned** | 100 |
| **Average Difficulty** | Beginner |
| **Primary Categories** | Web Exploitation |
| **Success Rate** | 100% |

---

## 🚀 Getting Started

### Prerequisites

To follow along with these write-ups, you should have:

- Basic understanding of web technologies (HTTP, URLs, HTML)
- Familiarity with Linux command line
- Access to common penetration testing tools (Kali Linux recommended)
- TryHackMe account (for attempting challenges)

### Recommended Setup

```bash
# Install Gobuster (if not already installed)
sudo apt update
sudo apt install gobuster

# Common wordlists location
/usr/share/wordlists/dirb/common.txt
/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# Verify installation
gobuster version
```

---

## 📖 Write-Up Structure

Each write-up in this repository follows a consistent structure:

1. **Overview** - Challenge description and objectives
2. **Challenge Information** - Technical details and target info
3. **Skills Required/Learned** - Prerequisites and takeaways
4. **Tools Used** - Software and utilities employed
5. **Methodology** - High-level approach phases
6. **Key Findings** - Critical discoveries made
7. **Attack Chain** - Visual flow of the exploitation process
8. **Detailed Walkthrough** - Step-by-step solution with screenshots
9. **Flags Captured** - Successfully obtained flags
10. **Vulnerabilities Discovered** - Security issues identified
11. **Lessons Learned** - Key takeaways and insights
12. **Security Recommendations** - How to prevent these vulnerabilities

---

## 🔒 Ethical Considerations

**Important Notice:**

- All challenges documented here were completed on **authorized platforms** (TryHackMe)
- Techniques shown are for **educational purposes only**
- **Never** use these techniques on systems without explicit authorization
- Follow responsible disclosure practices
- Respect the TryHackMe Terms of Service

### Responsible Hacking Guidelines

✅ **DO:**
- Practice on authorized CTF platforms
- Learn and document your findings
- Help others learn through sharing knowledge
- Report vulnerabilities responsibly

❌ **DON'T:**
- Attack systems without permission
- Use techniques for malicious purposes
- Share flags without spoiler warnings
- Violate platform rules or laws

---

## 📬 Contact & Social

- **TryHackMe Profile:** [hyena11](https://tryhackme.com/p/hyena11)
- **GitHub:** This Repository

---

## 🤝 Contributing

While this is a personal write-up repository, feedback and suggestions are welcome!

If you find any errors or have suggestions for improvement:
1. Open an issue with details
2. Provide constructive feedback
3. Respect the learning process

---

## 📜 License

This repository is for **educational purposes only**. The write-ups are original content created by hyena11.

- Write-up content: © 2026 hyena11
- CTF challenges: © TryHackMe
- Tools mentioned: Property of their respective owners

---

## 🙏 Acknowledgments

- **TryHackMe** - For providing an excellent learning platform
- **LAFB CTF 2026 Organizers** - For creating engaging challenges
- **The CTF Community** - For continuous knowledge sharing and support

---

## 📈 Progress Tracker

```
┌─────────────────────────────────────────┐
│  LAFB CTF 2026 Progress                 │
├─────────────────────────────────────────┤
│  Challenges Completed:  1               │
│  Current Streak:        33 Days         │
│  Points Earned:         100             │
│  Status:                Active          │
└─────────────────────────────────────────┘
```

---

## 🔄 Recent Updates

### February 16, 2026
- ✨ Added "Hidden Deep Into My Heart" write-up
- 📝 Initial repository setup
- 🎯 Completed first challenge of LAFB CTF 2026

---

## 📚 Additional Resources

### Learning Platforms
- [TryHackMe](https://tryhackme.com) - Interactive cybersecurity training
- [HackTheBox](https://hackthebox.com) - Penetration testing labs
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) - Free web security training

### Tool Documentation
- [Gobuster GitHub](https://github.com/OJ/gobuster) - Official repository
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/) - Web security testing methodology

### CTF Resources
- [CTFtime](https://ctftime.org) - CTF events calendar
- [PentesterLab](https://pentesterlab.com) - Hands-on pentesting exercises

---

<div align="center">

**⭐ If you found these write-ups helpful, consider giving this repo a star! ⭐**

Made with 💘 by hyena11 | TryHackMe LAFB CTF 2026

</div>
