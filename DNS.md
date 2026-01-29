# 🔎 DNS Reconnaissance & Enumeration — FULL DETAILED NOTES (EN)

## 1. What is DNS Reconnaissance?

**DNS Reconnaissance** is an information-gathering phase focused on collecting data about a target’s domain and infrastructure using DNS.

### Goals:

- Discover **subdomains**
    
- Identify **mail servers**
    
- Map **hostnames to IP addresses**
    
- Perform **reverse lookups**
    
- Expand the **attack surface**
    

> DNS recon does NOT exploit directly, but it enables almost every successful attack.

---

## 2. Reverse IP vs Reverse DNS

### Reverse IP

- Finds **all domains hosted on the same IP**
    
- Useful in shared hosting environments
    
- Can reveal additional targets
    

**Important note:**

> One IP address can be linked to **multiple domains**

---

### Reverse DNS (PTR Lookup)

- Maps **IP → hostname**
    
- Uses **PTR records**
    
- Depends on correct DNS configuration (not always available)
    

---

## 3. DNS Record Types (Must Know)

### NS — Name Server

- Identifies authoritative DNS servers
    
- Can reveal:
    
    - DNS provider
        
    - Internal naming patterns
        

---

### A Record

- Maps hostname → IPv4 address  
    Example:
    

`www.megacorpone.com → 51.222.169.200`

---

### AAAA Record

- Maps hostname → IPv6 address
    

---

### MX — Mail Exchange

- Identifies mail servers for a domain
    
- A domain can have **multiple MX records**
    
- Very useful for:
    
    - Email attacks
        
    - Phishing reconnaissance
        

---

### PTR Record

- Used for reverse DNS
    
- Maps IP → hostname
    

---

### CNAME

- Creates an alias for another hostname  
    Example:
    

`mail.example.com → server01.internal.local`

---

### TXT Record

- Arbitrary text data
    
- Common uses:
    
    - Domain ownership verification
        
    - SPF / DKIM / DMARC
        
- Sometimes leaks:
    
    - Internal domains
        
    - Email infrastructure details
        

---

## 4. Using `nslookup`

### Query a specific record from a specific DNS server

`nslookup -type=TXT info.megacorptwo.com 192.168.50.151`

**Explanation:**

- `-type=TXT` → record type
    
- `info.megacorptwo.com` → target domain
    
- `192.168.50.151` → DNS server
    

Useful in **internal networks**.

---

## 5. Bulk Reverse DNS Enumeration

`for ip in $(seq 200 254); do host 51.222.169.$ip; done | grep -v "not found"`

### What this does:

- Generates an IP range
    
- Performs reverse DNS lookups
    
- Filters empty results
    

Result:

- Valid hostnames
    
- Internal naming structure
    

---

## 6. hackervdns (Reverse DNS Tool)

### Purpose:

Automated reverse DNS enumeration for IP ranges

`prips CIDR | hackervdns -r 8.8.8.8`

**Explanation:**

- `prips CIDR` → generate IP addresses
    
- `hackervdns` → reverse DNS lookup
    
- `-r 8.8.8.8` → Google DNS resolver
    

---

## 7. Certificate Transparency (crt.sh)

### Why it matters:

- SSL certificates are publicly logged
    
- Any subdomain with a certificate may appear
    

### Reveals:

- Subdomains
    
- Old domains
    
- Internal naming conventions
    

Passive recon (no interaction with target).

---

## 8. Automated DNS Enumeration — dnsrecon

### Standard Enumeration

`dnsrecon -d megacorpone.com -t std`

Collects:

- NS
    
- SOA
    
- MX
    
- A records
    

---

### Bruteforce Enumeration

`dnsrecon -d megacorpone.com -D ~/list.txt -t brt`

- Uses a wordlist
    
- Discovers hidden subdomains
    

---

## 9. Nmap OS Guessing

`sudo nmap --osscan-guess -O <target>`

- Attempts OS detection
    
- Not always accurate
    

---

## 10. Windows PowerShell Enumeration

`Test-NetConnection -port <PORT> <IP>`

- Windows alternative to nmap
    
- Useful for internal assessments
    

---

## DNS Recon — Exam Notes (eJPT)

- DNS is always an early phase
    
- TXT records often leak information
    
- Reverse DNS is powerful in labs
    
- dnsrecon + crt.sh = high-value combo
    

---

## DNS Summary

DNS reconnaissance:

- Is passive and active
    
- Expands attack surface
    
- Enables later exploitation