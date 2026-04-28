# THMLianYu

## 📖 Overview
This lab focuses on reconnaissance, enumeration, and exploitation of a vulnerable machine hosted on TryHackMe. The objective is to identify hidden directories, extract credentials, and escalate privileges to obtain user and root flags.

---

## Tools Used
- **Nmap** – Network scanning and service enumeration
- **Gobuster** – Directory brute forcing
- **CyberChef** – Decoding encoded data
- **FTP client** – Accessing files via FTP
- **SSH client** – Remote login
- **Linux privilege escalation techniques** – Exploiting misconfigurations

---

## Step 1: Reconnaissance
Performed an Nmap scan:
```bash
nmap -sV -sC 10.49.170.154
