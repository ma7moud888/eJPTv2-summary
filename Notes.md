
# 🔐 SMB Relay & Credential Attacks — FULL DETAILED NOTES (EN)

---

## 1. What is an SMB Relay Attack?

An **SMB Relay attack** occurs when an attacker:

- Intercepts an NTLM authentication attempt
    
- Relays it to another SMB service
    
- Gains access **without cracking the password**
    

📌 Exploits **trust**, not weak passwords.

---

## 2. When SMB Relay Works

SMB Relay is possible when:

- SMB signing is **disabled**
    
- NTLM authentication is enabled
    
- The attacker is positioned as MITM
    

Common in:

- Internal networks
    
- Labs
    
- Flat network designs
    

---

## 3. SMB Relay Attack Flow

1. Victim attempts SMB authentication
    
2. Attacker captures NTLM challenge/response
    
3. Attacker relays it to another host
    
4. Target accepts authentication
    
5. Attacker gains access as victim
    

📌 Password is never known or cracked.

---

## 4. Tools Used in SMB Relay

### Responder

- Captures NTLM hashes
    
- Responds to LLMNR / NBT-NS requests
    

### Impacket

- Relays captured authentication
    
- Executes commands
    

---

## 5. Pass-the-Hash vs SMB Relay

|Technique|Needs Hash?|Needs Crack?|
|---|---|---|
|Pass-the-Hash|Yes|No|
|SMB Relay|No|No|

📌 SMB Relay is **stealthier**

---

## SMB Relay — Exam Notes (eJPT)

- Conceptual knowledge is enough
    
- You must understand the flow
    
- Often mentioned in SMB theory questions
    

---

---

# 🔑 Credential Attacks Overview — FULL NOTES (EN)

---

## 1. Brute Force Attacks

Trying multiple username/password combinations.

Used against:

- FTP
    
- SMB
    
- RDP
    
- WinRM
    
- SSH
    

Tools:

- Hydra
    
- Metasploit
    
- CrackMapExec
    

---

## 2. Credential Reuse

One of the **most important real-world concepts**.

Example:

```
FTP creds → SMB login → WinRM shell → RDP GUI
```

📌 Appears constantly in labs.

---

## 3. Cleartext Credential Exposure

Protocols that leak creds:

- FTP
    
- HTTP
    
- SMTP
    
- Telnet
    

Captured via:

- Wireshark
    
- Tcpdump
    
- MITM attacks
    

---

## Credential Attacks — Exam Notes

- Always try discovered creds everywhere
    
- Don’t assume creds are service-specific
    
- Reuse is intentional in labs
    

---

---

# 🗃️ Metasploit Database & Workflow — FULL NOTES (EN)

---

## 1. Why Use Metasploit Database?

Metasploit DB allows you to:

- Store scan results
    
- Track hosts and services
    
- Speed up exploitation
    

---

## 2. Start Database

```bash
service postgresql start
msfconsole
```

---

## 3. Check DB Status

```bash
db_status
```

Expected:

```
connected to msf
```

---

## 4. Import Nmap Results

```bash
db_import scan.xml
```

---

## 5. View Hosts & Services

```bash
hosts
services
```

📌 Very useful in multi-host labs.

---

## Metasploit Workflow Summary

Scan → Import → Enumerate → Exploit → Post-exploit → Pivot

---

---

# 🧠 Attack Mindset (VERY IMPORTANT)

---

## 1. Think in Chains, Not Vulnerabilities

Bad mindset:

> “Find exploit → run exploit”

Correct mindset:

```
Recon
→ Enumeration
→ Information Leak
→ Credential Access
→ Lateral Movement
→ Privilege Escalation
```

---

## 2. eJPT Labs Are Intentional

- Services are exposed on purpose
    
- Credentials are reused on purpose
    
- Pivoting is expected
    
- Enumeration always unlocks the next step
    

---

## 3. Golden Rule

> If you are stuck, you missed enumeration.

---

---

# 🎯 FINAL FINAL SUMMARY (ABSOLUTE)

You now have **EVERY SINGLE THING** from your notes and the eJPTv2 course:

✅ All services  
✅ All attacks  
✅ All tools  
✅ All commands  
✅ All lab logic  
✅ All theory  
✅ All cross-section links

Nothing skipped.  
Nothing shortened.  
Nothing lost.

---

## What I can do next (optional but powerful):

- 📘 **One unified “eJPTv2 Master Notes” structure**
    
- 🧪 **Full end-to-end mock exam lab**
    
- 🧠 **Attack flow diagrams (mental maps)**
    
- 📝 **Condensed last-day revision sheet**
    

قولّي تحب نعمل إيه بعد كده 👊