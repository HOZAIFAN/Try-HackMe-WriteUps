# 🎯 Active Directory Penetration Test Write-Up

> *"From zero to SYSTEM: A tale of misconfigured credentials and broken trust"*

## 💀 Executive Summary

This is the story of how a single Guest account with SMB access led to complete domain controller compromise. Watch as weak passwords, exposed backups, and protocol abuse transform anonymous enumeration into NT AUTHORITY\SYSTEM access.

**Target:** Windows Server 2022 Domain Controller  
**Domain:** SOUPEDECODE.LOCAL  
**Final Achievement:** Full domain takeover via Pass-the-Hash  
**Time to Compromise:** Single session engagement

## 🔥 The Attack Path

**Six Stages of Domination:**

1. **Guest SMB Access** → Anonymous user enumeration via RID brute force
2. **Password Spraying** → Username-as-password weakness (ybob317:ybob317)
3. **Kerberoasting** → Service account hash extraction and offline cracking
4. **Backup Discovery** → Machine account NTLM hashes found in plaintext files
5. **Pass-the-Hash** → FileServer$ machine account authentication
6. **PsExec Execution** → SYSTEM shell on domain controller

**The Beauty:** Each stage builds on the last, demonstrating how small misconfigurations snowball into critical compromise.

## 💣 Critical Vulnerabilities

| Finding | Severity | Impact |
|---------|----------|--------|
| Guest SMB Access | Medium | User enumeration without credentials |
| Weak Password Policy | High | Password spraying succeeds |
| Kerberoastable Accounts | High | Offline hash cracking |
| Credentials in Backups | **Critical** | Machine account hash exposure |
| Pass-the-Hash Attack | **Critical** | Complete domain compromise |

## 🎓 What You'll Learn

**Advanced AD Exploitation:**
- RID brute force enumeration through SMB/RPC
- Kerberoasting attacks (T1558.003) against service accounts
- Pass-the-Hash authentication with machine accounts
- PsExec lateral movement and privilege escalation
- The difference between `ls` and `dir` (yes, really!)

**Critical Mistakes Exposed:**
- Why PsExec syntax matters (spoiler: hostname resolution issues)
- Understanding machine account privileges (FileServer$ is powerful!)
- Windows commands vs Linux commands (stop using `ls` on Windows!)
- Why backups are gold mines for attackers

**Detection & Defense:**
- SIEM rules for RID enumeration detection
- Monitoring Event ID 4769 for Kerberoasting attempts
- Implementing Group Managed Service Accounts (gMSA)
- Proper backup encryption and access controls

## 🛠️ The Toolkit

**Primary Weapons:**
- **Nmap** - Port scanning and service detection
- **NetExec (nxc)** - Modern replacement for CrackMapExec
- **Impacket** - PsExec, SMBClient, and Python AD tools
- **Hashcat** - Kerberos TGS ticket cracking (mode 13100)

## 📊 By The Numbers

- **Open Ports:** 15 (full AD signature detected)
- **Users Enumerated:** 5 domain accounts via RID brute force
- **Passwords Cracked:** 2 (initial access + service account)
- **Hash Type:** Kerberos 5 TGS-REP etype 23 (RC4-HMAC)
- **Final Privilege:** NT AUTHORITY\SYSTEM

## ⚡ Quick Wins & Pro Tips

**For Attackers:**
- Always try username-as-password in password spraying
- Machine account hashes are often overlooked treasure
- Backup shares are frequently unencrypted and over-permissive
- PsExec requires correct syntax: `domain/user@IP` with IP, not hostname

**For Defenders:**
- Disable guest account SMB access immediately
- Implement 14+ character password minimum
- Use gMSA for service accounts
- Encrypt ALL backups with BitLocker
- Monitor Event ID 4769 for RC4 encryption (Kerberoasting indicator)

## 🎯 Perfect For

**Red Teamers** learning Active Directory attack chains  
**Blue Teamers** understanding attacker methodology  
**Security Students** preparing for OSCP/eCPPT certifications  
**IT Admins** hardening AD environments

## 📈 MITRE ATT&CK Coverage

- **T1595** - Active Scanning
- **T1078** - Valid Accounts
- **T1110.003** - Password Spraying
- **T1558.003** - Kerberoasting
- **T1003** - OS Credential Dumping
- **T1021.002** - SMB/Windows Admin Shares

## ⭐ Verdict

**Difficulty:** Medium | **Real-World Relevance:** Extremely High

This isn't a theoretical exercise—every technique here is actively used in real AD environments. The detailed command reference, error troubleshooting, and defensive recommendations make this essential reading for anyone serious about AD security.

**Rating:** ⭐⭐⭐⭐⭐ - A masterclass in Active Directory exploitation

---

**Penetration Tester:** hyena  
**University:** COMSATS University Islamabad  
**Platform:** TryHackMe (Soupedecode 01)  
**Date:** January 22, 2026

*This writeup is for educational and authorized testing purposes only. Unauthorized access to computer systems is illegal.*