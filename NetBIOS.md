
---

# 🖥️ NetBIOS Enumeration & Attacks — FULL DETAILED NOTES (EN)

## 1. What is NetBIOS?

**NetBIOS (Network Basic Input/Output System)** is a legacy interface/protocol that allows devices on a LAN to:

- Communicate using **device names**
    
- Share files and printers
    
- Establish sessions
    

Still appears in:

- Legacy Windows networks
    
- Labs and internal environments
    

---

## 2. NetBIOS over TCP/IP (NBT)

NetBIOS usually runs as:

> **NetBIOS over TCP/IP (NBT)**

### Common Ports:

|Port|Protocol|Service|
|---|---|---|
|137|TCP/UDP|Name Service|
|138|UDP|Datagram Service|
|139|TCP|Session Service|
|445|TCP|SMB (related)|

---

## 3. NetBIOS Core Services

### 1) Name Service (137)

- Registers and resolves NetBIOS names
    
- Maps device name ↔ IP
    

---

### 2) Datagram Service (138)

- Connectionless communication
    
- Broadcast-based
    
- Similar to UDP
    

---

### 3) Session Service (139)

- Reliable connection
    
- Required for SMBv1 file sharing
    

---

## 4. NetBIOS Name Structure (Exam Critical)

- NetBIOS name = **16 characters**
    
- First **15** → device name
    
- Last character → **service suffix (hex)**
    

### Common Suffixes:

|Hex|Meaning|
|---|---|
|0x00|Workstation|
|0x20|File Server|
|0x03|Messenger|

Names longer than 15 chars are **truncated**.

---

## 5. Security Impact of NetBIOS

- No encryption
    
- Legacy protocol
    
- Leaks:
    
    - Computer names
        
    - Domain/workgroup
        
    - Shared services
        

Excellent for **internal reconnaissance**.

---

## 6. Enumeration with `nbtstat` (Windows)

### Query by IP

```bash
nbtstat -A 192.168.1.10
```

---

### Show local NetBIOS table

```bash
nbtstat -n
```

---

### Show active sessions

```bash
nbtstat -s
```

---

### Query statistics

```bash
nbtstat -r
```

---

### Clear name cache

```bash
nbtstat -R
```

---

### Reload LMHOSTS

```bash
nbtstat -RR
```

---

### Query by name

```bash
nbtstat -a SERVER1
```

---

### Verbose output

```bash
nbtstat -v
```

---

## 7. NetBIOS Enumeration with Nmap

```bash
nmap -p 137,138,139,445 <target>
```

### NSE Script

```bash
nmap --script nbstat.nse -p 137 <target>
```

Reveals:

- NetBIOS names
    
- Domain info
    
- MAC address (sometimes)
    

---

## 8. NetBIOS Datagram Service (Details)

- Runs on UDP/138
    
- No delivery guarantee
    
- Heavy broadcast usage
    
- Deprecated in modern networks
    

---

## 9. NetBIOS Session Service (Details)

- Runs on TCP/139
    
- Reliable connection
    
- Required for SMBv1
    

### Flow:

1. Session request
    
2. Acceptance
    
3. Data exchange
    
4. Session termination
    

---

## 10. Attack Scenarios Involving NetBIOS

- Information disclosure
    
- SMB attacks (Relay, Pass-the-Hash)
    
- Lateral movement
    
- Internal network mapping
    

---

## NetBIOS — Exam Notes (eJPT)

- NetBIOS = reconnaissance tool
    
- Presence often implies:
    
    - Legacy systems
        
    - SMB misconfigurations
        
- Common precursor to SMB exploitation
    

---

## NetBIOS Summary

NetBIOS is:

- Not an exploit itself
    
- A powerful internal recon mechanism
    
- Common in eJPT labs
    

---

🔥 **Next topics (EN, same depth):**

- SNMP Enumeration & Exploitation
    
- WebDAV
    
- SMB
    
- FTP
    
- IIS / Tomcat
    
- Pivoting
    

Say the next one and I’ll continue immediately.