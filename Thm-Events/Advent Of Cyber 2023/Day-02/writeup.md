[🏠 Back to Event](../README.md)
---

# 🎄 Day 02: Data Science with Python

> Category: Data Science / Network Analysis
> Difficulty: Easy

---

## 📌 Overview

This challenge introduces the basics of **data science in cybersecurity** using Python, Pandas, and Jupyter Notebooks.

The objective was to analyze a packet capture dataset (`network_traffic.csv`) to better understand the activity occurring within AntarctiCrafts’ South Pole network.

---

## 🔍 Key Steps

* Loaded the packet capture dataset into a Pandas DataFrame
* Analyzed packet counts using the `PacketNumber` column
* Identified the source IP generating the most traffic
* Determined the most frequently used network protocol
* Used Pandas functions such as:

  * `count()`
  * `value_counts()`

---

## 🛠️ Tools Used

* Python 3
* Jupyter Notebook
* Pandas
* Matplotlib

---

## ⚡ Solution Summary

The dataset was analyzed using Pandas inside the provided Jupyter Notebook.

### Total Packets Captured

```python id="b4x92f"
df["PacketNumber"].count()
```

**Answer:** `100`

---

### IP Address with Most Traffic

```
df["Source"].value_counts()
```

**Answer:** `10.10.0.1`

---

### Most Frequent Protocol

```
df["Protocol"].value_counts()
```

**Answer:** `ICMP`

---

## 🧾 Code

The full notebook/code used for this challenge can be found here:

```
./Workbook.ipynb
```

---

## 🧠 Key Takeaway

* Data science techniques can simplify network traffic analysis
* Pandas is extremely useful for processing and investigating datasets
* Even basic statistical analysis can reveal important network insights

---

## 💬 Offensive Security Relevance

> This challenge demonstrates:
>
> * Network traffic analysis using Python
> * Identifying active hosts and communication patterns
> * How attackers and defenders can use data analysis during reconnaissance and monitoring

---

## 📎 Notes (Optional)

* Jupyter Notebooks are commonly used for:

  * Threat hunting
  * Malware analysis
  * Log analysis
  * Security research
* Pandas is widely used in SOC environments for parsing large datasets

---


---

## 🔗 Navigation

| ⬅️ Previous | ➡️ Next |
|------------|--------|
| [Day 01 - Chatbot,tell me,if you're really safe?](../Day-01/writeup.md) | [Day 03 - Hydra is Coming to Town](../Day-03/writeup.md) |

---