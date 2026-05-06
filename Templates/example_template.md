# 🧪 Room Name: Basic Pentesting

> Difficulty: Easy
> Category: Network / PrivEsc
> Platform: TryHackMe

---

## 📌 Overview

This room focuses on basic penetration testing techniques against a Linux machine.
The goal is to enumerate services, gain initial access, and escalate privileges.

* Target: Linux machine
* Skills: Enumeration, SSH access, privilege escalation

---

## 🎯 Objectives

* Perform network enumeration
* Identify vulnerabilities in exposed services
* Gain initial access
* Escalate privileges to root

---

## 🔍 Reconnaissance

Initial scan to identify open ports and services.

### Actions

* Performed service and version scan
* Identified exposed services

### Commands

```bash
nmap -sC -sV -oN scan.txt <TARGET_IP>
```

### Findings

* Open ports: 22 (SSH), 80 (HTTP)
* Services:

  * SSH (OpenSSH)
  * Apache Web Server
* Initial attack surface:

  * Web server (port 80)
  * SSH login

---

## 🧪 Enumeration

Further investigation of the web server.

### Actions

* Directory brute forcing
* Checked for hidden pages

### Commands

```bash
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt
```

### Findings

* Discovered hidden directory
* Found information related to a user account
* Possible username identified

---

## ⚔️ Exploitation

Using discovered credentials to gain access.

### Approach

* Brute-forced SSH login using discovered username
* Weak password allowed access

### Commands / Payloads

```bash
ssh <username>@<TARGET_IP>
```

### Result

* Access gained as: low-privileged user
* Shell access obtained

---

## 🔓 Privilege Escalation

Escalating privileges to root.

### Enumeration

* Checked for SUID binaries
* Looked for misconfigured permissions

### Findings

* Writable files or misconfigured services
* Possible privilege escalation vector identified

### Exploit

```bash
# Example (depends on method used)
sudo -l
```

### Result

* Privilege escalated to: root

---

## 🧠 Attack Path Summary

1. Recon → Found SSH and HTTP services
2. Enumeration → Discovered hidden directory and username
3. Exploitation → Gained SSH access using weak credentials
4. PrivEsc → Escalated privileges via misconfiguration

---

## 📚 Lessons Learned

### Key Takeaways

* Enumeration is critical before exploitation
* Weak credentials are a common attack vector
* Misconfigurations often lead to privilege escalation

### What Could Be Improved

* Faster identification of attack vectors
* Better wordlist selection

### Defensive Insights

* Enforce strong passwords
* Restrict SSH access
* Properly configure file permissions

---

## 💬 Offensive Security Relevance (GitHub Comment)

> This room demonstrates real-world offensive security concepts including:
>
> * Network and service enumeration
> * Credential attacks against SSH
> * Privilege escalation through misconfigurations
>
> It highlights how attackers chain small weaknesses (information disclosure + weak passwords + misconfigurations) to gain full system access.

---

## 📎 Notes

* Could also use Hydra for brute force:

```bash
hydra -l <username> -P wordlist.txt ssh://<TARGET_IP>
```

* Always enumerate thoroughly before exploiting
