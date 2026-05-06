# 🧪 Room Name: <ROOM_NAME>

> Difficulty: <Easy / Medium / Hard / Insane>
> Category: <Web / Network / PrivEsc / AD / Mixed>
> Platform: TryHackMe

---

## 📌 Overview

Brief description of the room:

* What is the main objective?
* What type of environment (Linux/Windows/Web)?
* Key skills required

---

## 🎯 Objectives

* <Objective 1>
* <Objective 2>
* <Objective 3>

---

## 🔍 Reconnaissance

Initial information gathering and observations.

### Actions

* <What you did first>
* <Tools used>

### Commands

```bash
# Example
nmap -sC -sV -oN scan.txt <TARGET_IP>
```

### Findings

* Open ports:
* Services:
* Initial attack surface:

---

## 🧪 Enumeration

Detailed investigation of discovered services.

### Actions

* <Directory brute force / service enumeration>
* <Version checks / vulnerability research>

### Commands

```bash
# Example
gobuster dir -u http://<TARGET_IP> -w wordlist.txt
```

### Findings

* Interesting endpoints:
* Credentials discovered:
* Misconfigurations:

---

## ⚔️ Exploitation

Gaining initial access.

### Approach

* <Explain the vulnerability exploited>
* <Why it works>

### Commands / Payloads

```bash
# Example
exploit code / reverse shell / payload
```

### Result

* Access gained as: <user>
* Proof (optional screenshot):

---

## 🔓 Privilege Escalation

Escalating privileges to root/administrator.

### Enumeration

* <LinPEAS / WinPEAS / manual checks>

### Findings

* <SUID files / weak permissions / cron jobs>

### Exploit

```bash
# Commands used for privesc
```

### Result

* Privilege escalated to: <root / administrator>

---

## 🧠 Attack Path Summary

Step-by-step chain:

1. Recon → Found <service>
2. Enumeration → Discovered <vulnerability>
3. Exploitation → Gained <initial access>
4. PrivEsc → Escalated to <root/admin>

---

## 📚 Lessons Learned

### Key Takeaways

* <What you learned>
* <New tools or techniques>

### What Could Be Improved

* <Mistakes / inefficiencies>

### Defensive Insights

* <How this could be prevented in real systems>

---

## 💬 Offensive Security Relevance (GitHub Comment)

> This room demonstrates real-world offensive security concepts including:
>
> * <e.g., service enumeration, web exploitation, privilege escalation>
> * Highlights how attackers chain misconfigurations to gain full system access
> * Reinforces structured penetration testing methodology from recon to root

---

## 📎 Notes

* <Extra observations>
* <Alternative approaches>
* <Useful references>
