# 🎄 Day 01: Chatbot,tell me,if you're really safe?

> Category: AI / Web Security
> Difficulty: Easy

---

## 📌 Overview

This challenge introduces **prompt injection attacks** against an internal chatbot (Van Chatty).
The goal is to understand how insecure NLP-based systems can be manipulated to leak sensitive information.

---

## 🔍 Key Steps

* Interacted with the chatbot and tested basic queries
* Asked direct questions to extract sensitive information (e.g., personal email)
* Bypassed restrictions by **impersonating authorized users**
* Used **prompt manipulation (e.g., “maintenance mode”)** to override protections

---

## 🛠️ Tools Used

* Web browser
* Built-in chatbot interface

---

## ⚡ Solution Summary

The chatbot was vulnerable to **prompt injection**, allowing sensitive data extraction by:

* Asking direct questions (no filtering)
* Impersonating trusted roles (e.g., IT staff)
* Manipulating context (e.g., “maintenance mode”) to bypass safeguards

---

## 🧠 Key Takeaway

* Chatbots can leak sensitive data if not properly secured
* System prompts are **not strict security controls**
* Input validation and filtering are critical

---

## 💬 Offensive Security Relevance

> This challenge demonstrates:
>
> * Prompt injection attacks against AI systems
> * How attackers manipulate trust and context
> * The risks of exposing sensitive data in AI training datasets

---

## 📎 Notes (Optional)

* Similar concept to **social engineering**, but targeting AI instead of humans
* Defenses include:

  * Input filtering (interceptors)
  * Strong system prompt design
  * Avoiding sensitive data in training datasets

---

---

## 🔗 Navigation

⬅️ Previous: N/A  
➡️ Next: [Day 02 - O Data, All Ye Faithful](../Day-02/writeup.md)

---

---

## 🔗 Navigation

| ⬅️ Previous | ➡️ Next |
|------------|--------|
| N/A | [Day 02](../Day-02/writeup.md) |

---
