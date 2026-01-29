

——

# 📁 FTP Enumeration & Attacks — FULL DETAILED NOTES (EN)

---

## 1. What is FTP?

**FTP (File Transfer Protocol)** is a **client–server protocol** used to transfer files over a network.

### Default Port

- **TCP 21**
    

FTP is widely used but **insecure by design** if not configured properly.

---

## 2. FTP Authentication Modes

### 1️⃣ Anonymous FTP

- Allows login **without credentials**
    
- Common username:
    
    ```
    anonymous
    ```
    
- Password often:
    
    ```
    anonymous
    ```
    

📌 Very common misconfiguration in labs

---

### 2️⃣ Authenticated FTP

- Requires valid username + password
    
- Often vulnerable to:
    
    - Weak passwords
        
    - Password reuse
        
    - Brute force attacks
        

---

## 3. FTP Security Weaknesses

- Credentials sent in **cleartext**
    
- No encryption
    
- Weak or default credentials
    
- Anonymous access
    
- Misconfigured permissions
    
- Vulnerable FTP software versions
    

---

## 4. FTP Enumeration with Nmap

### Detect FTP service & version

```bash
nmap -sV -p 21 <target>
```

---

### Check Anonymous Login

```bash
nmap -p 21 --script ftp-anon <target>
```

---

### FTP Banner Grab

```bash
nmap -p 21 --script ftp-banner <target>
```

Reveals:

- FTP software
    
- Version
    
- OS hints
    

---

## 5. Manual FTP Enumeration

### Connect to FTP

```bash
ftp <target>
```

---

### Anonymous Login

```
Name: anonymous
Password: anonymous
```

---

### Common FTP Commands

```bash
ls
pwd
cd
get file.txt
put file.txt
```

📌 Always look for:

- Config files
    
- Credentials
    
- Backup files
    
- Web roots
    

---

## 6. FTP Enumeration with Metasploit

### Detect FTP Version

```bash
use auxiliary/scanner/ftp/ftp_version
set RHOSTS <target>
run
```

---

## 7. FTP Brute Force Attacks

### Metasploit FTP Login

```bash
use auxiliary/scanner/ftp/ftp_login
set RHOSTS <target>
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
run
```

---

### Example Result

```
[+] Login Successful: sysadmin:654321
```

---

## 8. Vulnerable FTP Software (Lab Example)

### ProFTPD 1.3.5a

- Known backdoor vulnerability
    
- Common in labs
    

📌 Always check:

```bash
searchsploit proftpd
```

---

## 9. FTP → RCE Attack Chain

FTP access → Upload file  
→ Web directory write  
→ Webshell execution  
→ Reverse shell

📌 Happens when FTP shares the same directory as the web server

---

## 10. FTP → Credential Reuse

Credentials discovered via FTP often work on:

- SSH
    
- SMB
    
- RDP
    
- WinRM
    

---

