# ValenFind - TryHackMe Write-Up

![TryHackMe](https://img.shields.io/badge/TryHackMe-ValenFind-red)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner%2FIntermediate-green)
![Status](https://img.shields.io/badge/Status-Completed-success)
![Category](https://img.shields.io/badge/Category-Web%20Exploitation-blue)

## 📋 Table of Contents

- [Overview](#overview)
- [Room Information](#room-information)
- [Skills Required](#skills-required)
- [Skills Learned](#skills-learned)
- [Tools Used](#tools-used)
- [Methodology](#methodology)
- [Write-Up Structure](#write-up-structure)
- [Key Findings](#key-findings)
- [Attack Chain](#attack-chain)
- [Vulnerabilities Discovered](#vulnerabilities-discovered)
- [Files Included](#files-included)
- [Screenshots](#screenshots)
- [How to Use](#how-to-use)
- [Lessons Learned](#lessons-learned)
- [Security Recommendations](#security-recommendations)
- [Author](#author)
- [References](#references)

---

## 🎯 Overview

This repository contains a comprehensive write-up for the **ValenFind** challenge on TryHackMe. ValenFind is a beginner/intermediate difficulty web application penetration testing challenge that simulates a dating site with multiple security vulnerabilities. The room focuses on **Local File Inclusion (LFI)** , **source code analysis**, **hardcoded credential discovery**, and **admin API abuse**.

The challenge demonstrates how a single vulnerability (LFI) can cascade into complete application compromise when combined with poor security practices like hardcoded secrets and unprotected admin endpoints.

---

## 📊 Room Information

| Property | Value |
|----------|-------|
| **Platform** | TryHackMe |
| **Challenge Name** | ValenFind |
| **Difficulty** | Beginner / Intermediate |
| **Challenge Type** | Web Application Exploitation |
| **Target IP** | 10.48.131.200 |
| **Target Port** | 5000 |
| **Technology Stack** | Python/Flask, SQLite |
| **Completion Date** | February 16, 2026 |
| **Player Name** | hyena11 |
| **Final Access Level** | Full Database Export (Flag Extraction) |

---

## 🔧 Skills Required

- Basic understanding of **web technologies** (HTTP, URLs, parameters)
- Familiarity with **Linux command line**
- Knowledge of **directory enumeration** techniques
- Basic understanding of **web vulnerabilities** (LFI, XSS)
- Ability to read and analyze **source code**
- Familiarity with **SQLite databases**

---

## 📚 Skills Learned

### Enumeration Techniques
- **Port scanning** with Nmap
- **Directory fuzzing** with ffuf
- **Web application mapping** and endpoint discovery
- **Parameter analysis** for vulnerability identification

### Web Exploitation
- **Local File Inclusion (LFI)** discovery and exploitation
- **Path traversal** techniques and payloads
- **Source code disclosure** via LFI
- **Hardcoded credential** identification
- **Admin endpoint** abuse

### Post-Exploitation
- **Database extraction** and analysis
- **SQLite querying** for flag retrieval
- **Data exfiltration** techniques

### Security Analysis
- **Vulnerability chaining** concepts
- **Risk assessment** and severity classification
- **Remediation strategies** for found vulnerabilities

---

## 🛠️ Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| **Nmap** | 7.94 | Port scanning and service detection |
| **ffuf** | 2.1.0 | Directory fuzzing and content discovery |
| **cURL** | 7.88.1 | HTTP requests and file downloads |
| **Burp Suite** | 2024.1 | Request interception and manipulation |
| **SQLite3** | 3.40.1 | Database analysis and flag extraction |
| **Python** | 3.9 | Understanding application logic |
| **Browser DevTools** | - | Network analysis and debugging |

---

## 🎯 Methodology

### Phase 1: Reconnaissance
- Initial host discovery and OS fingerprinting
- Comprehensive port scanning
- Service version detection

### Phase 2: Enumeration
- Directory fuzzing to discover endpoints
- Web application mapping
- Parameter analysis and testing

### Phase 3: Vulnerability Discovery
- XSS probing (initial testing)
- LFI identification and verification
- File enumeration and source code retrieval

### Phase 4: Exploitation
- Source code analysis
- Hardcoded credential discovery
- Admin endpoint exploitation
- Database extraction

### Phase 5: Post-Exploitation
- Database analysis
- Flag extraction
- Documentation and reporting

---

## 🔍 Key Findings

### Vulnerabilities Identified

| # | Vulnerability | Severity | CVSS | Location | Impact |
|---|---------------|----------|------|----------|--------|
| **1** | **Local File Inclusion (LFI)** | **HIGH** | 8.6 | `/api/fetch_layout?layout=` | Arbitrary file read, source code disclosure |
| **2** | **Hardcoded Admin API Key** | **CRITICAL** | 9.8 | `app.py` (source code) | Authentication bypass, admin access |
| **3** | **Insecure Direct Object Access** | **HIGH** | 8.1 | `/api/admin/export_db` | Database export without proper authorization |
| **4** | **Information Disclosure** | **MEDIUM** | 5.3 | Multiple endpoints | Directory listing, user enumeration |
| **5** | **Missing Input Validation** | **MEDIUM** | 6.1 | Profile fields | XSS potential, parameter manipulation |

### Critical Findings Summary

| Finding | Value |
|---------|-------|
| **Hardcoded API Key** | `CUPID_MASTER_KEY_2024_XOXO` |
| **Database Location** | `valenfind.db` |
| **Admin Username** | `admin_root_x99` |
| **Captured Flag** | `THM{v1be_c0ding_1s_n0t_my_cup_0f_t3a}` |

---


---

📚 Lessons Learned
For Attackers
Always test file inclusion parameters - they're goldmines
Error messages often leak critical information
Source code disclosure leads to secret discovery
Chain vulnerabilities - one hole leads to another

For Defenders
Never trust user input - validate everything
Never hardcode credentials in source code
Implement proper access controls for admin endpoints
Use environment variables for all secrets
Regular security code reviews are essential

<h2>👤 Author<\h2>

Name	hyena11
Platform	TryHackMe
Role	Penetration Tester / CTF Player
Completion Date	February 16, 2026

<h2>📊 Statistics<\h2>

Metric	Value
Time to Complete	~45 minutes
Vulnerabilities Found	5
Tools Used	7
Commands Executed	25+
Files Exfiltrated	8
Last Updated: February 16, 2026
Status: ✅ Complete



