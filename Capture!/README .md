#Capture!

# Login Brute Force Attack - Security Lab

A web application security lab demonstrating username enumeration and password brute force vulnerabilities with captcha bypass.

---

## Overview

This repository contains a Python script that demonstrates common authentication vulnerabilities in web applications. The script automates the process of finding valid login credentials through username enumeration and password brute forcing, including automated captcha solving.

---

## Features

- Automated username enumeration based on server error messages
- Password brute forcing against discovered usernames
- Automatic math captcha solving
- Custom URL support
- Colored console output for better visibility


---

## Requirements

Python 3.x with the following packages:

```
requests
termcolor
```

Install dependencies:

```bash
pip install requests termcolor
```

---

## Usage

**Basic usage:**

```bash
python3 capture.py
```

**Custom target URL:**

```bash
python3 capture.py --url http://target-ip/login
```

**Wordlist format:**

`usernames.txt`
```
admin
administrator
root
user
test
natalie
rachel
```

`passwords.txt`
```
123456
password
admin
football
qwerty
letmein
```

---

## How It Works

1. **Username Enumeration** — Tests usernames from the wordlist and identifies valid ones by analyzing different error messages.
2. **Captcha Solving** — Extracts math problems from the page and calculates answers automatically.
3. **Password Brute Force** — Attempts passwords from the wordlist against the valid username.
4. **Credential Output** — Displays found credentials when successful.

---

## Example Output

```
[INFO] : you are processing : http://10.113.180.230/login
--------------------------------------------------
[*] Starting username enumeration...
[+] Valid username found: rachel
------------------------------
[*] Starting password brute force for rachel...
[+] Valid password found for username : rachel
username : rachel
password : football
use these creds to login and get your flag
------------------------------
[+] Attack completed successfully!
```

---

## Vulnerabilities Demonstrated

- Username enumeration through verbose error messages
- Lack of rate limiting on login attempts
- Weak captcha implementation (simple math)
- Common password usage

---

## Mitigation Strategies

- Use generic error messages for all login failures
- Implement rate limiting and account lockouts
- Use strong captcha services (e.g. reCAPTCHA)
- Enforce strong password policies
- Consider multi-factor authentication

---

## Important Notes

**Educational Purpose Only.** This tool is designed for security education and authorized testing only. Only use on systems you own or have explicit permission to test. Designed for controlled CTF (Capture The Flag) environments.

Unauthorized use against systems you do not own may violate laws and regulations.

---

## Contributing

Feel free to submit issues or pull requests for improvements or bug fixes.
