
---

# 🧩 Microsoft IIS Enumeration & Exploitation — FULL DETAILED NOTES (EN)

---

## 1. What is IIS?

**IIS (Internet Information Services)** is a **Microsoft web server** running on Windows systems.

### Common Usage:

- Hosting websites
    
- Running web applications
    
- ASP / ASP.NET applications
    
- APIs and internal services
    

### Default Ports:

- **80/TCP** (HTTP)
    
- **443/TCP** (HTTPS)
    

---

## 2. IIS File Types & Extensions (VERY IMPORTANT)

### ASP

- `.asp`
    
- Classic Active Server Pages
    
- Executed by IIS engine
    

### ASP.NET

- `.aspx`
    
- `.ashx`
    
- `.asmx`
    
- `.axd`
    

📌 These files are **EXECUTED**, not served as static files.

---

### Config Files

- `web.config`
    
- `machine.config`
    

📌 If exposed → **critical information disclosure**

Possible leaks:

- Connection strings
    
- Credentials
    
- Debug settings
    
- Authentication modes
    

---

### PHP on IIS

- IIS can run `.php` if PHP is installed
    
- Same vulnerabilities as Apache PHP:
    
    - RCE
        
    - File upload
        
    - Command injection
        

---

## 3. IIS Request Processing Flow (Exam Favorite)

```
File on Disk
   ↓
IIS Web Server
   ↓
Handler Selection (based on extension)
   ↓
Execution Engine (ASP.NET / PHP)
   ↓
HTML Output
   ↓
Response to Client
```

📌 Key idea:

> If attacker uploads an executable file into a **handled directory**, it WILL execute.

---

## 4. IIS Enumeration with Nmap

### Basic Service Detection

```bash
nmap -sV -p 80 demo.ine.local
```

---

### IIS Vulnerability Scan

```bash
nmap --script http-enum -p 80 <target>
```

Finds:

- `/webdav`
    
- `/uploads`
    
- `/admin`
    
- `/phpinfo.php`
    

---

## 5. IIS Enumeration with Nikto

```bash
nikto -h http://<target>
```

Detects:

- Dangerous files
    
- Misconfigurations
    
- Outdated IIS versions
    

---

## 6. File Upload Vulnerabilities on IIS

### Core Idea:

If IIS allows uploading:

- `.asp`
    
- `.aspx`
    

➡️ **Remote Code Execution**

---

### Common Upload Locations:

- `/uploads`
    
- `/files`
    
- `/webdav`
    
- `/images` (misconfigured)
    

---

## 7. IIS + WebDAV (Cross-Section Link)

If WebDAV is enabled on IIS:

- PUT allowed
    
- Executable extensions allowed
    

➡️ Upload `.aspx` webshell  
➡️ RCE

📌 This connects **WebDAV section + IIS section**

---

## 8. IIS Exploitation — Webshell Example

### Example Webshell Access

```
http://target/webdav/webshell.aspx?cmd=whoami
```

Output:

```
nt authority\system
```

---

## 9. IIS Exploitation Tools

### Common Tools:

- Nikto
    
- Burp Suite
    
- Metasploit
    
- Webshells (ASPX)
    

---

### Metasploit Example

```bash
search iis
```

```bash
use exploit/windows/iis/<module>
```

---

## 10. IIS → Privilege Escalation Path

IIS RCE → www-data / iis user  
→ Local privilege escalation  
→ SYSTEM  
→ Lateral movement (SMB / WinRM)

---

## IIS — Exam Notes (eJPT)

- IIS = Windows target
    
- `.aspx` execution is critical
    
- web.config exposure = jackpot
    
- Combine with WebDAV
    
- Often leads to SYSTEM
    

---

## IIS Summary

- IIS is powerful
    
- Misconfiguration = RCE
    
- Strong pivot point in internal networks
    

---

🔥 **NEXT (still very interlinked across sections):**

➡️ **Apache Tomcat Enumeration & Exploitation (CVE-2017-12617)**  
➡️ بعده مباشرة:

- RDP
    
- WinRM
    
- Pivoting & Lateral Movement
    

أنا هكمّل **Tomcat دلوقتي في نفس الرد** ولا تحب واحد واحد؟