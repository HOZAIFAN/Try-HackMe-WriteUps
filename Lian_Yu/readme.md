# 🏹 LianYu CTF Write-Up

> *"You have failed this city!"* - But you won't fail this CTF.

Welcome to my complete walkthrough of **LianYu**, an Arrowverse-themed medium difficulty box from TryHackMe that'll test your hacking skills from web enumeration to root access.

## 🎭 The Story

Ever wondered what it's like to hack an island? This Arrow Season 1-inspired machine takes you on a journey through Lian Yu, where you'll decode secret tokens, repair corrupted files, extract hidden messages from images, and ultimately escalate your way to root. No prior knowledge of the show required - just bring your hacking toolkit and detective mindset.

## 🎯 The Journey

**From Zero to Root in 11 Steps:**

What starts as a simple port scan evolves into a multi-layered puzzle involving Base58 decoding, file forensics, steganography, and privilege escalation. You'll discover hidden tickets, repair broken PNG headers byte-by-byte, extract passwords from images, and exploit sudo misconfigurations to claim both flags.

**The Highlights:**
- 🔍 Hunt for hidden `.ticket` files using clever wordlists
- 🔧 Manually repair corrupted PNG headers with hexadecimal surgery
- 🖼️ Crack steganography challenges to unlock SSH credentials  
- 👑 Exploit pkexec to become DEATHSTROKE and capture the root flag

## 🚩 Mission Accomplished

**User Flag:** `THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}`  
**Root Flag:** `THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT...}`

## 💡 What Makes This Special

This isn't just another CTF writeup - it's a detailed forensic analysis that shows you the *why* behind every technique. Learn how PNG file signatures work, understand the difference between encoding and encryption, and discover why sudo misconfigurations are a security nightmare.

**Perfect for:** Intermediate hackers looking to sharpen their enumeration skills and learn real-world exploitation techniques wrapped in an engaging narrative.

## ⭐ Verdict

**5/5** - A masterclass in CTF design that balances education with entertainment. The Arrowverse theme isn't just window dressing - it cleverly guides your exploitation path while keeping you engaged.

---

**Author:** hyena | **Completed:** January 22, 2026  
*For educational purposes only. Always hack responsibly and with authorization.*