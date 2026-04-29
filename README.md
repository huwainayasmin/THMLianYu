# THMLianYu

## Overview
This lab focuses on reconnaissance, enumeration, and exploitation of a vulnerable machine hosted on TryHackMe. The objective is to identify hidden directories, extract credentials, and escalate privileges to obtain user and capture flags (proof of access).

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
Scanned the IP address using:
```bash
nmap 10.49.170.154
```
Findings:
- **21 (FTP)**
- **22 (SSH)**
- **80 (HTTP)**
- **111(RPC)**
<img width="903" height="380" alt="image" src="https://github.com/user-attachments/assets/2ed3ceb1-a946-4ecc-a866-cad77b9f2108" />

---

## Step 2: Web Enumeration
Visit the target IP in a web browser
```bash
http://10.49.170.154
```
<img width="895" height="739" alt="image" src="https://github.com/user-attachments/assets/ca29ae8e-0a3b-4d32-81d8-535a4ef9a1a4" />

Ran gobuster to find hidden directories
```bash
gobuster dir -u http://10.49.170.154 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
Found directory: /island
<img width="948" height="518" alt="image" src="https://github.com/user-attachments/assets/818f8305-99c5-460d-a728-feda7fa87616" />


Visit 
```bash
http://10.49.165.124/island/
```
<img width="899" height="734" alt="image" src="https://github.com/user-attachments/assets/49641db9-5544-438e-afe7-e2a08e0284f8" />


View the page source for more hints
<img width="886" height="479" alt="image" src="https://github.com/user-attachments/assets/e71e54ba-de98-47e0-8b01-836e9d532ab0" />


Found the code word 'vigilante' (FTP username).
<img width="623" height="56" alt="image" src="https://github.com/user-attachments/assets/03eecbdb-0b9c-41be-917c-ccc17d1233b6" />


Dig deeper into /island by running gobuster on /island
```bash
gobuster dir -u http://10.49.170.154/island -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
Found directory: /2100
<img width="937" height="512" alt="image" src="https://github.com/user-attachments/assets/795e71b1-9e0b-48ce-a8df-01c918443b57" />

Visit 
```bash
http://10.49.170.154/island/2100
```
<img width="956" height="861" alt="Screenshot 2026-04-29 071642" src="https://github.com/user-attachments/assets/365c0d6f-5833-4311-8a8f-1cc52774c25f" />

View the page source
<img width="955" height="736" alt="Screenshot 2026-04-29 072145" src="https://github.com/user-attachments/assets/e337d04c-793f-424f-a2ab-bd621197c992" />

Ran gobuster on /2100
```bash
gobuster dir -u http://10.49.165.124/island/2100/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x ticket
```
Found directory: green_arrow.ticket
<img width="932" height="543" alt="image" src="https://github.com/user-attachments/assets/5e1ce35d-5e13-4a61-995a-5578b94874ab" />

## Step 3: File Discovery
Visit
```bash
http://10.49.170.154/island/2100/green_arrow.ticket
```
Found a token 'RTy8yhBQdscX'.
<img width="716" height="267" alt="image" src="https://github.com/user-attachments/assets/cc6f4d5c-bf25-4028-a922-d54769347aaa" />

## Step 4: Credential Extraction
Visit CyberChef to decode the token
```bash
https://gchq.github.io/CyberChef/
```
Use Code58 to decrypt the token and get '!#th3h00d' which is the FTP password
<img width="1919" height="871" alt="image" src="https://github.com/user-attachments/assets/5444c5fd-2a75-4353-82f0-25bb97513607" />


> [!NOTE]
> From this point on, the IP address has changed because I used a vpn in my Kali Linux instead of the AttackBox in TryHackMe

## Step 5: FTP Access
Log in through ftp using the username and password found earlier
```bash
ftp 10.48.137.168
```
<img width="335" height="197" alt="image" src="https://github.com/user-attachments/assets/a83656f2-ec21-4f0a-9812-53c5a7f8e4d1" />

Use the 'ls' command to display hidden files
<img width="606" height="124" alt="image" src="https://github.com/user-attachments/assets/d546bacb-dc8d-475c-858c-0cf6896cbe0b" />

Use the command 'mget *' to download all the files we found
<img width="649" height="338" alt="image" src="https://github.com/user-attachments/assets/151308c6-bc8f-4fb0-aa34-06d27089a3a7" />




















