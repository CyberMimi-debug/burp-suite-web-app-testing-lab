# burp-suite-web-app-testing-lab
Web application penetration testing lab — full LAMP stack deployment, Burp Suite proxy interception and Cluster Bomb brute force attack against DVWA on Kali Linux
# Burp Suite Web Application Testing Lab — DVWA

Advanced Ethical Hacking 
**Team: **  Sparta  
**Target: ** Damn Vulnerable Web Application (DVWA)  
**Tools: ** Burp Suite Community Edition v2025.5.3, Kali Linux, MariaDB, Apache

---

## Overview

This lab involved building a complete web application attack environment from scratch and using Burp Suite to perform a credential brute force attack against DVWA. Rather than using a pre-configured environment, the full LAMP stack was installed and configured manually before any testing began.

---

## Environment Setup

### LAMP Stack Installation

The entire web server environment was built from the ground up on Kali Linux:

- Started the MySQL service and connected to MariaDB
- Created the DVWA database with `CREATE DATABASE dvwa`
- Created a dedicated database user `dbcooper` with full privileges
- Edited the DVWA configuration file at `/var/www/html/DVWA/config/config.inc.php` to set database credentials and default security level
- Configured the Apache virtual host at `/etc/apache2/sites-enabled/000-default.conf`
- Restarted Apache with `sudo systemctl restart apache2` and verified it was active and running
- Confirmed DVWA was accessible at `127.0.0.1/DVWA/login.php`

### Burp Suite Proxy Configuration

Before intercepting any traffic, Firefox was configured to route all HTTP and HTTPS traffic through Burp Suite:

- Set manual proxy to `127.0.0.1` on port `8080`
- Enabled the same proxy for HTTPS traffic
- Confirmed Burp Suite was intercepting all browser requests

---

## Attack — Credential Brute Force with Cluster Bomb

### Step 1 — Intercept the Login Request

With Burp Suite intercept enabled, a login attempt was made on the DVWA brute force page. Burp captured the GET request containing the username and password parameters in the URL. The request was right-clicked and sent to Intruder.

### Step 2 — Configure Intruder

Attack type was set to **Cluster Bomb** — this tests every combination of two payload lists against the two marked positions in the request (username and password fields).

**Payload 1 — Usernames:**
admin, root, user, test, guest, sparta, hacker, login, dvwa, webuser

**Payload 2 — Passwords:**
123456, password, test, guest, spartans, dvwa, qwerty, 1111, letmein, webpass

Total combinations: 100 requests

### Step 3 — Launch Attack and Identify Valid Credentials

The attack fired all 100 combinations. By sorting results by response length, request 46 stood out — confirming the valid credential pair:

**Username:** sparta  
**Password:** spartans

The raw request confirmed: `GET /dvwa/vulnerabilities/brute/?username=sparta&password=spartans&Login=Login`

---

## Key Findings

- DVWA was vulnerable to brute force attacks at its default low security setting with no account lockout or rate limiting in place
- The valid credentials were identified within 46 requests out of 100 — a real attacker with a larger wordlist (such as rockyou.txt) would crack this in seconds
- No CAPTCHA or multi-factor authentication was present to slow or prevent automated login attempts

---

## Blue Team Observations

- Account lockout policies should be enforced — repeated failed login attempts from the same IP should trigger a lockout or CAPTCHA
- Rate limiting on login endpoints would significantly slow brute force attacks
- Web application firewalls (WAF) can detect and block Intruder-style automated request patterns
- Login attempts should be logged and monitored — a spike in 302 redirects from a single source is a clear indicator of a brute force attempt

---

## Tools Used

- [Burp Suite Community Edition](https://portswigger.net/burp) v2025.5.3
- [DVWA](https://github.com/digininja/DVWA) — Damn Vulnerable Web Application
- Kali Linux
- MariaDB / Apache2
- Firefox with manual proxy configuration

---

## Skills Demonstrated

- Full LAMP stack deployment and configuration
- Web proxy setup and HTTP traffic interception
- Burp Suite Intruder — Cluster Bomb attack configuration
- Credential brute force attack execution and analysis
- Vulnerability identification and defensive recommendations

---

## Related Projects

- [MITRE CALDERA Adversary Emulation Lab](#)
- [Memory Forensics with Volatility](#)
- [Splunk Detection Engineering Lab](#)

---

> **Disclaimer:** All activity was performed in an isolated virtual environment for educational purposes as part of coursework at George Brown College. No real systems or networks were targeted.

