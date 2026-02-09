============================================================
        AGENT SUDO - TRYHACKME WALKTHROUGH
============================================================

   >>> Beginner to Intermediate CTF Write-up <<<
   >>> Enumeration | Bruteforce | Stego | Privesc <<<

============================================================

Author Information
------------------
Author Name        : Japi
TryHackMe Username : japi
Platform Used      : Kali Linux
Room Name          : Agent Sudo (TryHackMe Exclusive)
Difficulty Level   : Beginner -> Intermediate

============================================================
OBJECTIVE
============================================================

The goal of this room is to revise and apply core
cybersecurity concepts in a single realistic attack chain.

You will learn how to:
 - Enumerate open services
 - Bypass web restrictions
 - Bruteforce weak credentials
 - Extract hidden data from files
 - Gain SSH access
 - Escalate privileges using a real CVE

This room is perfect for strengthening fundamentals.

============================================================
TOOLS USED
============================================================

[+] Operating System
-------------------
  Kali Linux
  -> Main attacking machine

------------------------------------------------------------

[+] Enumeration & Recon
-----------------------
  Nmap
   -> Port scanning
   -> Service & version detection

  Curl
   -> User-Agent manipulation
   -> Web access bypass

------------------------------------------------------------

[+] Authentication Attacks
--------------------------
  Hydra
   -> FTP brute-force using rockyou.txt

  FTP
   -> File enumeration & downloads

------------------------------------------------------------

[+] File Analysis & Forensics
-----------------------------
  Binwalk
   -> Detect hidden files in images
   -> Extract embedded ZIP archives

  Zip2John
   -> Extract ZIP password hash

  John The Ripper
   -> Crack ZIP password

------------------------------------------------------------

[+] Steganography
-----------------
  Steghide
   -> Extract hidden data from JPG images

------------------------------------------------------------

[+] File Transfer
-----------------
  SCP
   -> Secure copy files from target machine

------------------------------------------------------------

[+] Privilege Escalation
-----------------------
  Sudo
   -> Misconfigured sudo permissions

  CVE-2019-14287
   -> !root bypass
   -> Spawn root shell

============================================================
ATTACK FLOW (VISUAL)
============================================================

   [ NMAP SCAN ]
         |
         v
   [ WEB ENUMERATION ]
         |
         v
   [ USER-AGENT BYPASS ]
         |
         v
   [ FTP BRUTE FORCE ]
         |
         v
   [ FILE DOWNLOAD ]
         |
         v
   [ BINWALK ZIP EXTRACTION ]
         |
         v
   [ PASSWORD CRACKING ]
         |
         v
   [ STEGANOGRAPHY ]
         |
         v
   [ SSH ACCESS ]
         |
         v
   [ SUDO MISCONFIG ]
         |
         v
   [ ROOT SHELL 👑 ]

============================================================
SKILL COVERAGE METER
============================================================

 Enumeration        [##########] 100%
 Web Exploitation   [########--] 80%
 Bruteforce         [########--] 80%
 Steganography      [#######---] 70%
 Privilege Escal.   [##########] 100%

============================================================
PROBLEMS FACED & SOLUTIONS
============================================================

[!] FTP password unknown
 -> Solved using Hydra brute-force

[!] ZIP file extraction failed
 -> Used 7z instead of unzip

[!] Encoded text unreadable
 -> Decoded using CyberChef (Base64)

[!] User-Agent confusion
 -> Tested alphabetically (A -> Z)

[!] Sudo looked restricted
 -> Exploited CVE-2019-14287

============================================================
WHY THIS ROOM MATTERS
============================================================

This room teaches an important lesson:

"One weak password or one misconfiguration
 is enough to fully compromise a system."

Agent Sudo connects theory with real-world exploitation
in a simple and enjoyable way.

============================================================
FINAL NOTES
============================================================

 - Excellent room for beginners
 - Great revision before harder CTFs
 - Covers multiple attack vectors
 - Highly recommended

Next Write-up:
 -> Wireshark CTFs (Part 2)

============================================================
END OF FILE
============================================================
