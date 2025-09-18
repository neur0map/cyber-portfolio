# Underpass - total time 2 days

Nmap scan

```sql
nmap -sC -sV -p- 10.10.11.48
```

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image.png)

and then i ran 

```sql
nmap -sU --top-ports 200 10.10.11.48
```

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%201.png)

I added the address to /etc/hosts 

<aside>
💡

sudo nano /etc/hosts

10.10.11.48        underpass.htb

</aside>

After investigating what the ports were and meant i researched commands to get information out of the ports and i ran.

```sql
snmpwalk -v2c -c public 10.10.11.48
```

SNMP (Simple Network Management Protocol) collects and organizes information about devices on IP networks for monitoring and management.

The `snmpwalk` command retrieves SNMP information through chained GETNEXT requests.

Flag meanings:

- `-v2c`: Uses SNMP version 2c for improved error handling and bulk retrieval
- `-c public`: Uses 'public' as the default read-only access password

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%202.png)

After investigating what daloradius server is i found common login credentials and i tried to open the site underpass.htb/daloradius but it took no where

I ran ferofoxer, read it works better than gobuster

```sql
feroxbuster -u http://underpass.htb/daloradius -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50
```

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%203.png)

I tried each address and underpass.htb/daloradius/app/operators/ worked, it took me to a login page where i tested the default passwords first and got access to the server (app/users/ also has login but credentials didnt work)

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%204.png)

Once inside you can see the user and password but its encrypted

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%205.png)

Running hashid on the hash to see if would give me possible hash types it game quite a few so i started from the bottom MD2 didnt work and luckly i didnt have to go down too much, MD5 worked.
and i ran

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%206.png)

```sql
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

- `-m 0`: Specifies MD5 hash mode
- `-a 0`: Sets attack mode to straight/dictionary attack
- `hash.txt`: Input file containing the hash to crack
- `/usr/share/wordlists/rockyou.txt`: Path to the wordlist file used for cracking

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%207.png)

After getting the password I can try connecting to the ssh which it worked

```sql
ssh svcMosh@underpass.htb
password:underwaterfriends
```

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%208.png)

User flag was right there with ls and cat command

Getting privilage escalation was difficult as always but extensive research i found a solution

```sql
sudo -l
```

The `sudo -l` command lists the sudo privileges for the current user, showing what commands they can run with elevated permissions.

then

```sql
sudo /usr/bin/mosh-server
```

this showed the mosh server id and port, which after researching i found an exploit https://medium.com/@momo334678/mosh-server-sudo-privilege-escalation-82ef833bb246

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%209.png)

after using the found exploit i was finally able to gain root and get the flag

![image.png](Underpass%20-%20total%20time%202%20days%201df5b4e7124c80a390c7c6bdabe5c330/image%2010.png)