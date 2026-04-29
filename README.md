# THMLianYu

## Overview
This is the work of Huwaina Yasmin binti Anuar Asfandi, student ID: 52215124317, class: Vulnerability Analysis: L01-B02
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

The Leave_me_alone.png contains errors and cannot be opened
<img width="441" height="154" alt="image" src="https://github.com/user-attachments/assets/2119472e-5fad-4b2d-b132-4d6b2a252439" />

So I exited ftp mode and used the command 'hexeditor' to see the errors.
<img width="1112" height="625" alt="image" src="https://github.com/user-attachments/assets/bce180cf-b839-4a35-a72e-31b5558c53ae" />

Changed the png header according to the right one
<img width="985" height="546" alt="image" src="https://github.com/user-attachments/assets/964e563f-4bff-426b-86b5-3c385e232d27" />
<img width="783" height="289" alt="image" src="https://github.com/user-attachments/assets/8512e048-24c5-43c1-895f-36bb17aae64e" />
> Image source: https://en.wikipedia.org/wiki/PNG

Once the Leave_me_alone.png is opened, we get the password for steghide: password (😂).The password is for steghide because steghide can be used for jpgs
```bash
steghide extract -sf aa.jpg
```
<img width="841" height="510" alt="image" src="https://github.com/user-attachments/assets/c9f56c96-a640-498f-a0da-5c222095d633" />

Upon using the password, we extracted an 'ss.zip' file. After unzipping, we get a 'passwd.txt' file and a 'shado' file.
<img width="278" height="82" alt="image" src="https://github.com/user-attachments/assets/1c2da41a-d856-4cf6-b9be-c87aea0cad85" />
<img width="210" height="95" alt="image" src="https://github.com/user-attachments/assets/e58af727-89a1-4b1b-b944-d614f70241a5" />

Now using the 'cat' command on the 'shado' file to display the entire content of the file. We get the password for an SSH file: M3tahuman
```bash
cat shado
```
<img width="201" height="59" alt="image" src="https://github.com/user-attachments/assets/ffea9f1e-3b15-4108-8e84-9826ee4e9aac" />

We can now log in for the user slade's SSH file.
```bash
ssh slade@10.48.137.168
```
<img width="655" height="580" alt="image" src="https://github.com/user-attachments/assets/eca437f1-2a67-46eb-b6d8-495129377693" />

Use the command 'ls' to display all files contained...and we get a 'user.txt' file. Use the command 'cat user.txt' to display the contents. 
We can see the message 'THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}'
<img width="340" height="95" alt="image" src="https://github.com/user-attachments/assets/e8ef540c-2b85-47c3-b68e-48942b73fee5" />

Use the command ' sudo pkexec su' to access root privileges
```bash
sudo pkexec su
```
<img width="250" height="40" alt="image" src="https://github.com/user-attachments/assets/600e4e47-ca8e-4322-a84f-2d3e8209fd54" />

Navigate to the root directory and use the command 'ls' to display all the files...we get a 'root.txt' file. 
<img width="214" height="49" alt="image" src="https://github.com/user-attachments/assets/872e3436-1d61-4beb-8dfb-e3acaed43319" />

Use the command 'cat root.txt' to display the contents...and we get a 'Mission Accomplished' message!!!
<img width="744" height="255" alt="image" src="https://github.com/user-attachments/assets/82729273-8026-4e8a-b8df-b2f70670fe16" />

> I completed this room with the help of this YouTube video: https://youtu.be/_K6P8lJA-io?si=l-DsKIGYOJ8vZwqi































