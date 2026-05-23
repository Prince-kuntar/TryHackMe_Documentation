# Penetration Test Report  
## RecruitX Web Application Assessment

---

# Document Control

| Field | Value |
|---|---|
| Client | RecruitX |
| Assessment Type | Web Application Penetration Test |
| Assessment Date | 22 May 2026 |
| Tester | Prince |
| Environment | TryHackMe Lab |
| Report Version | 1.0 |

---

# Executive Summary

A penetration test was conducted against the RecruitX web application to identify security weaknesses within the platform.

During the assessment, multiple critical vulnerabilities were identified that allowed a complete compromise of the application. The attack chain began with information disclosure and insecure direct object references (IDOR), escalated into administrator account takeover through a weak password reset mechanism, and ultimately resulted in remote code execution (RCE) on the server.

The vulnerabilities identified indicate insufficient access control enforcement, insecure authentication workflows, and unsafe file upload handling.

An attacker exploiting these weaknesses could:

- Access sensitive user information
- Compromise privileged accounts
- Execute arbitrary commands on the server
- Potentially pivot deeper into the internal infrastructure

Immediate remediation is strongly recommended.

---

# Assessment Scope

## Target

| IP Address | Description |
|---|---|
| 10.48.169.161 | RecruitX Web Application |

---

# Objectives

The assessment focused on:

- Reconnaissance and enumeration
- Access control testing
- Authentication testing
- File upload testing
- Remote code execution testing

---

# Methodology

The assessment followed a standard penetration testing methodology consisting of:

1. Reconnaissance
2. Enumeration
3. Vulnerability Identification
4. Exploitation
5. Post-Exploitation Validation
6. Reporting

Testing was performed using manual techniques combined with industry-standard security tools.

---

# Tools Used

| Tool | Purpose |
|---|---|
| Nmap | Port scanning and service enumeration |
| Gobuster | Directory enumeration |
| Curl | HTTP/API interaction |
| Netcat | Reverse shell listener |
| PHP Reverse Shell | Remote command execution |

---

# Technical Findings

---

# Finding 1 — Information Disclosure via API Enumeration

| Field | Value |
|---|---|
| Severity | Medium |
| CWE | CWE-200 |
| OWASP | A01:2021 – Broken Access Control |

---

## Description

The application exposed internal API endpoints without authentication or authorization controls.

Accessing:

```text
/api/
```

revealed internal routes including:

```json
{
  "endpoints":[
    "/api/user",
    "/api/jobs",
    "/api/applications"
  ]
}
```

This disclosure expanded the attack surface and provided intelligence useful for further exploitation.

---

## Impact

An attacker could:

- Discover hidden functionality
- Map application architecture
- Identify attack vectors
- Enumerate sensitive endpoints

---

## Evidence

```bash
curl http://10.48.169.161/api/
```

📸 Screenshot: `screenshots/api-enum.png`

---

## Recommendation

- Require authentication for API discovery endpoints
- Disable unnecessary endpoint disclosure
- Implement proper API authorization controls

---

# Finding 2 — Insecure Direct Object Reference (IDOR)

| Field | Value |
|---|---|
| Severity | High |
| CWE | CWE-639 |
| OWASP | A01:2021 – Broken Access Control |

---

## Description

The application used predictable numeric identifiers to reference user profiles:

```text
/profile.php?id=7
```

Modifying the identifier allowed unauthorized access to profiles belonging to other users, including administrators.

The same vulnerability was also present within the API endpoint:

```text
/api/user?id=1
```

---

## Impact

An attacker could:

- Enumerate users
- Access sensitive user information
- Identify privileged accounts
- Gather intelligence for privilege escalation

---

## Evidence

```bash
curl -s http://10.48.169.161/api/user?id=1
```

Response:

```json
{
  "id":1,
  "name":"Sarah Mitchell",
  "role":"administrator"
}
```

📸 Screenshot: `screenshots/idor-admin-profile.png`

---

## Recommendation

- Enforce server-side authorization checks
- Validate ownership before returning objects
- Replace sequential identifiers with UUIDs where possible

---

# Finding 3 — Weak Password Reset Mechanism

| Field | Value |
|---|---|
| Severity | Critical |
| CWE | CWE-640 |
| OWASP | A07:2021 – Identification and Authentication Failures |

---

## Description

The password reset functionality contained multiple critical flaws:

- Reset tokens consisted of only six digits
- Reset tokens were displayed directly within the application response

These weaknesses allowed administrator account takeover.

Compromised account:

```text
s.mitchell@recruitx.thm
```

Password changed to:

```text
654321
```

---

## Impact

An attacker could fully compromise arbitrary user accounts, including administrators.

---

## Evidence

The password reset process exposed reset tokens directly to the user interface.

📸 Screenshot: `screenshots/password-reset.png`

---

## Recommendation

- Use cryptographically secure reset tokens
- Store tokens server-side only
- Never expose tokens in responses
- Implement expiration timers
- Enforce rate limiting

---

# Finding 4 — Unrestricted File Upload

| Field | Value |
|---|---|
| Severity | Critical |
| CWE | CWE-434 |
| OWASP | A05:2021 – Security Misconfiguration |

---

## Description

The administrator upload functionality claimed to restrict uploads to:

- PDF
- PNG
- JPG
- DOCX

However, the application accepted `.phtml` files, allowing executable PHP code to be uploaded to the server.

Uploaded payload:

```text
shell.phtml
```

---

## Impact

An attacker could upload malicious server-side scripts and execute arbitrary code on the server.

---

## Evidence

The uploaded file was accessible through:

```text
/uploads/documents/shell.phtml
```

📸 Screenshot: `screenshots/admin-upload.png`

---

## Recommendation

- Strictly validate file extensions
- Validate MIME types server-side
- Store uploads outside the web root
- Disable script execution in upload directories
- Implement content scanning

---

# Finding 5 — Remote Code Execution (RCE)

| Field | Value |
|---|---|
| Severity | Critical |
| CWE | CWE-94 |
| OWASP | A05:2021 – Security Misconfiguration |

---

## Description

Using the unrestricted file upload vulnerability, a PHP reverse shell was uploaded and executed on the server.

A Netcat listener was established:

```bash
nc -lvnp 1234
```

The uploaded payload was triggered through:

```text
http://10.48.169.161/uploads/documents/shell.phtml
```

Resulting in successful remote shell access.

---

## Impact

An attacker could:

- Execute arbitrary commands
- Access sensitive files
- Establish persistence
- Pivot deeper into the environment

---

## Evidence

Successful reverse shell connection established.

📸 Screenshot: `screenshots/reverse-shell.png`

---

## Recommendation

- Prevent executable uploads
- Disable PHP execution within upload directories
- Harden web server configurations
- Implement application allowlists

---

# Attack Chain Summary

The following attack path was successfully demonstrated:

1. API Enumeration
2. User Enumeration via IDOR
3. Administrator Identification
4. Password Reset Abuse
5. Administrator Account Takeover
6. Malicious File Upload
7. Remote Code Execution

This demonstrates how multiple lower-level weaknesses can be chained together into a complete system compromise.

---

# Risk Summary

| Finding | Severity |
|---|---|
| Information Disclosure | Medium |
| IDOR | High |
| Weak Password Reset | Critical |
| Unrestricted File Upload | Critical |
| Remote Code Execution | Critical |

---

# Overall Risk Rating

# 🔴 Critical

The RecruitX application is vulnerable to complete compromise through multiple exploitable weaknesses.

Immediate remediation is strongly recommended before deployment into production environments.

---

# Remediation Priorities

## Immediate Actions

- Disable executable uploads
- Patch password reset functionality
- Enforce authorization checks
- Review administrative access controls

---

## Long-Term Recommendations

- Conduct secure code reviews
- Implement centralized authorization
- Perform regular penetration testing
- Adopt secure development lifecycle practices
- Harden server configurations

---

# Conclusion

The RecruitX web application contained multiple critical vulnerabilities that allowed a complete compromise of the platform.

The assessment demonstrated how insufficient access controls, insecure authentication workflows, and unsafe file upload handling can be chained together by attackers to gain full server access.

Addressing the identified issues will significantly improve the security posture of the application and reduce the likelihood of compromise.

---

# Appendix

## Artefacts Identified

| Artefact | Location |
|---|---|
| Uploaded shell | `/uploads/documents/shell.phtml` |
| Modified admin password | `654321` |

---

# End of Report