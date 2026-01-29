


---

# 🖥️ RDP Enumeration & Attacks — FULL DETAILED NOTES (EN)

---

## 1. What is RDP?

**RDP (Remote Desktop Protocol)** is a **Microsoft proprietary protocol** that allows **remote GUI access** to Windows systems.

### Purpose:

- Remote administration
    
- User remote login
    
- System management
    

---

## 2. RDP Ports

- **Default:** `3389/TCP`
    
- Can be changed (security through obscurity)
    

📌 RDP exposed to a network = **high-value target**

---

## 3. RDP Discovery & Enumeration

### Basic Port Scan

```bash
nmap -p 3389 <target>
```

---

### Full Port Scan (Non-standard RDP ports)

```bash
nmap -p 1-65535 <target> --open
```

---

### Service Detection

```bash
nmap -sV -p 3389 <target>
```

---

## 4. Network Level Authentication (NLA)

### What is NLA?

**Network Level Authentication** requires users to authenticate **before** a full RDP session is created.

### Pros:

- Reduces DoS attacks
    
- Reduces resource usage
    

### Cons:

- Blocks older RDP clients
    
- Prevents some brute-force tools
    

📌 Labs may disable NLA

---

## 5. RDP Client — xfreerdp

### Install

```bash
sudo apt-get install freerdp2-x11
```

---

### Basic Login

```bash
xfreerdp /v:<IP> /u:<USER> /p:<PASS>
```

---

### Custom Port

```bash
xfreerdp /v:<IP>:<PORT> /u:<USER> /p:<PASS>
```

---

### Disable NLA

```bash
xfreerdp /v:<IP> /u:<USER> /p:<PASS> -sec-nla
```

---

### Share Local Drive

```bash
xfreerdp /v:<IP> /u:<USER> /p:<PASS> /drive:SHARE,/path/local
```

📌 Useful for file transfer during post-exploitation

---

## 6. RDP Attacks

### 1️⃣ Brute Force

- Weak credentials
    
- Password reuse
    

Tools:

- Hydra
    
- Metasploit
    

---

### 2️⃣ Credential Reuse

- Credentials from:
    
    - FTP
        
    - SMB
        
    - SNMP
        
- Often reused on RDP
    

---

### 3️⃣ Known Vulnerabilities

- **BlueKeep (CVE-2019-0708)**
    
- Pre-authentication RCE
    
- Rare but exam-relevant concept
    

---

## RDP — Exam Notes (eJPT)

- Always scan for RDP
    
- Credentials = instant GUI access
    
- Combine with SMB / SNMP creds
    
- GUI access = easy privilege escalation
    

---

## RDP Summary

- RDP = direct system control
    
- Credential-based attacks are most common
    
- High-impact service
    

---

---

# 🔐 WinRM Enumeration & Exploitation — FULL DETAILED NOTES (EN)

---

## 1. What is WinRM?

**WinRM (Windows Remote Management)** is a Microsoft protocol used for **remote command execution and administration**.

Based on:

- HTTP / HTTPS
    
- WS-Management standard
    

---

## 2. WinRM Ports

|Port|Protocol|
|---|---|
|5985|HTTP|
|5986|HTTPS|

📌 Very common in **internal Windows networks**

---

## 3. WinRM Discovery

```bash
nmap -p 5985,5986 <target>
```

---

## 4. WinRM Authentication

- NTLM
    
- Kerberos
    
- Certificate-based (rare in labs)
    

📌 Requires valid credentials  
📌 Very common after SMB / SNMP enumeration

---

## 5. WinRM Enumeration with CrackMapExec

### Brute Force

```bash
crackmapexec winrm <target> -u users.txt -p passwords.txt
```

---

### Command Execution

```bash
crackmapexec winrm <target> -u user -p pass -x "whoami"
```

---

## 6. Evil-WinRM (Most Important Tool)

### Basic Connection

```bash
evil-winrm -i <IP> -u <USER> -p <PASS>
```

---

### Custom Port

```bash
evil-winrm -i <IP> -u <USER> -p <PASS> -s <PORT>
```

---

### Features

- Interactive PowerShell shell
    
- File upload/download
    
- Script execution
    
- Post-exploitation ready
    

📌 Evil-WinRM = **Shell equivalent to SSH for Windows**

---

## 7. WinRM → Full System Control

WinRM access allows:

- Command execution
    
- User enumeration
    
- Credential dumping
    
- Lateral movement
    
- Privilege escalation
    

---

## WinRM — Exam Notes (eJPT)

- Often appears with SMB
    
- Requires creds (reuse is key)
    
- Evil-WinRM is mandatory knowledge
    
- Preferred over RDP in stealth ops
    

---

## WinRM Summary

- WinRM = remote PowerShell
    
- High-value lateral movement vector
    
- Extremely common in labs
    

---

---

# 🔁 Pivoting & Lateral Movement — FULL DETAILED NOTES (EN)

---

## 1. What is Pivoting?

**Pivoting** is the technique of using a **compromised system** to access **internal networks** that are otherwise unreachable.

---

## 2. Why Pivoting Matters

- Real networks are segmented
    
- Initial access ≠ full access
    
- Internal services often:
    
    - Have weak security
        
    - Trust internal IPs
        

---

## 3. Discover Internal Networks

### From Meterpreter

```bash
ipconfig
```

or

```bash
ip addr
```

Example:

```
eth0 → 192.63.4.3
eth1 → 192.180.108.2
```

---

## 4. Metasploit Autoroute (Core Lab Skill)

```bash
run autoroute -s 192.180.108.0/24
```

Purpose:

- Route traffic through compromised host
    
- Access internal network
    

---

## 5. Internal Scanning via Pivot

```bash
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.180.108.3
set PORTS 1-1000
run
```

---

## 6. Pivoting Attack Chain

Initial host compromise  
→ Discover second interface  
→ Autoroute  
→ Internal scan  
→ Exploit second host

📌 This pattern appears **multiple times in labs**

---

## 7. Pivoting Tools

- Metasploit (autoroute)
    
- Proxychains
    
- SSH tunneling
    
- Meterpreter SOCKS proxy
    

---

## Pivoting — Exam Notes (eJPT)

- Always check network interfaces
    
- Internal IP ≠ internet-facing
    
- Autoroute is mandatory
    
- Expect multi-host labs
    

---

## Final Global Summary

You now have **ALL CORE eJPTv2 TOPICS**, fully merged across sections:

- DNS
    
- NetBIOS
    
- SNMP
    
- WebDAV
    
- SMB
    
- FTP
    
- IIS
    
- Tomcat
    
- RDP
    
- WinRM
    
- Pivoting
    

📌 Nothing skipped  
📌 Nothing shortened  
📌 Everything exam-ready

If you want next:

- 📘 **One combined “Exam Cheat Sheet”**
    
- 🧠 **Attack Flow Mindmaps**
    
- 🧪 **End-to-End Lab Walkthroughs**
    
- 📝 **Bug-bounty style reports**
    

Say the word 👊