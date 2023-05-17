First enumeration with nmap 

```
# Nmap 7.93 scan initiated Wed May 17 12:13:10 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80 -Pn 10.10.10.6
Nmap scan report for 10.10.10.6
Host is up (0.16s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.1p1 Debian 6ubuntu2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 3ec81b15211550ec6e63bcc56b807b38 (DSA)
|_  2048 aa1f7921b842f48a38bdb805ef1a074d (RSA)
80/tcp open  http    Apache httpd 2.2.12 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.2.12 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed May 17 12:13:24 2023 -- 1 IP address (1 host up) scanned in 14.03 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/34f2bb05-236d-4107-95e9-4f812e523e1c)

noticed a web server running on port 80, openning it on a browser, got directed to a default web page. let's fuzz for hidden dirs.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Popcorn] 
└─$ dirb http://10.10.10.6/                                                                                                                                            
                                                                                                                                                                       
-----------------                                                                                                                                                      
DIRB v2.22                                                                                                                                                             
By The Dark Raver                                                                                                                                                      
-----------------                                                                                                                                                      
                                                                                                                                                                       
START_TIME: Wed May 17 11:43:45 2023                                                                                                                                   
URL_BASE: http://10.10.10.6/                                                                                                                                           
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt                                                                                                                   
                                                                                                                                                                       
-----------------                                                                                                                                                      
                                                                                                                                                                       
GENERATED WORDS: 4612                                                                                                                                                  
                                                                                                                                                                       
---- Scanning URL: http://10.10.10.6/ ----                                                                                                                             
+ http://10.10.10.6/cgi-bin/ (CODE:403|SIZE:286)                                                                                                                       
+ http://10.10.10.6/index (CODE:200|SIZE:177)                                                                                                                          
+ http://10.10.10.6/index.html (CODE:200|SIZE:177)                                                                                                                     
+ http://10.10.10.6/server-status (CODE:403|SIZE:291)                                                                                                                  
+ http://10.10.10.6/test (CODE:200|SIZE:47330)                                                                                                                         
==> DIRECTORY: http://10.10.10.6/torrent/ 

---- Entering directory: http://10.10.10.6/torrent/ ----                                                                                                               
==> DIRECTORY: http://10.10.10.6/torrent/admin/                                                                                                                        
+ http://10.10.10.6/torrent/browse (CODE:200|SIZE:9278)                                                                                                                
+ http://10.10.10.6/torrent/comment (CODE:200|SIZE:936)                                                                                                                
+ http://10.10.10.6/torrent/config (CODE:200|SIZE:0)
```
cool we have a juicy dir `torrent`, let's check it out.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/818c8009-fae9-4ced-8fe2-f57b84f03ab2)

navigating to the torrent dir, got directed to a torrent hoster cms. let's try to login.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2a79dbbc-fc62-46d0-ac5b-75dac33f7cd4)

tried to bypass the login page using sqli payload `admin' or 1=1-- :admin' or 1=1-- `...

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/80fd833d-d2ac-4f51-a0bb-1f2fbc90094c)

Bull's eye it worked, we are in as admin. let's check for how to exploit this service `Torrent Hoster`.





