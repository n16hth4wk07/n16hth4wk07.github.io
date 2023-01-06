Enumeration with nmap 

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Fri Jan  6 20:00:29 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.108.125                                               
Warning: 192.168.108.125 giving up on port because retransmission cap hit (10).                                                                                        
Increasing send delay for 192.168.108.125 from 320 to 640 due to 11 out of 29 dropped probes since last increase.                                                      
Increasing send delay for 192.168.108.125 from 640 to 1000 due to 11 out of 27 dropped probes since last increase.                                                     
Nmap scan report for 192.168.108.125                                                                                                                                   
Host is up (0.15s latency).                                                                                                                                            
Not shown: 65490 filtered tcp ports (no-response)                                                                                                                      
PORT      STATE  SERVICE 
8080/tcp  open   http-proxy
12445/tcp open   unknown
18030/tcp open   unknown
43022/tcp open   unknown
Read data files from: /usr/bin/../share/nmap
# Nmap done at Fri Jan  6 20:02:43 2023 -- 1 IP address (1 host up) scanned in 134.39 seconds
```

Let's run default nmap scripts to know what services are running on those ports.

`nmap -sC -sV -oN normal.tcp -p 43022,18030,12445,8080 -Pn <IP>`

```
# Nmap 7.93 scan initiated Wed Dec 28 23:47:19 2022 as: nmap -sC -sV -oN normal.tcp -p 43022,18030,12445,8080 -Pn 192.168.108.125                               [72/75]
Nmap scan report for 192.168.108.125                                                                                                                                   
Host is up (0.20s latency).                                                                                                                                            
                                                                                                                                                                      
PORT      STATE SERVICE     VERSION                                                                                                                                   
8080/tcp  open  http-proxy                                                                                                                                            
| fingerprint-strings:                                                                                                                                                
|   GetRequest:                                                                                                                                                       
|     HTTP/1.1 200                                                                                                                                                    
|     Content-Type: text/html;charset=UTF-8                                                                                                                           
|     Content-Language: en-US                                                                                                                                         
|     Content-Length: 3762                                                                                                                                            
|     Date: Wed, 28 Dec 2022 22:47:26 GMT                                                                                                                             
|     Connection: close                                                                                                                                               
|     <!DOCTYPE HTML>                                                                                                                                                 
|     <!--                                                                                                                                                            
|     Minimaxing by HTML5 UP                                                                                                                                          
|     html5up.net | @ajlkn                                                                                                                                            
|     Free for personal and commercial use under the CCA 3.0 license (html5up.net/license)                                                                            
|     <html>                                                                                                                                                          
|     <head>                                                                                                                                                          
|     <title>My Haikus</title>                                                                                                                                        
|     <meta charset="utf-8" />                                                                                                                                        
|     <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no" />                                                                        
|     <link rel="stylesheet" href="/css/main.css" />                                                                                                                  
|     </head>                                                                                                                                                         
|     <body>                                                                                                                                                          
|     <div id="page-wrapper">                                                                                                                                         
|     <!-- Header -->                                                                                                                                                 
|     <div id="header-wrapper">                                                                                                                                       
|     <div class="container">                                                                                                                                         
|     <div class="row">                                                                                                                                               
|     <div class="col-12">                                                                                                                                            
|     <header id="header">                                                                                                                                            
|     <h1><a href="/" id="logo">My Haikus</a></h1>
|     </header>
|     </div>
|     </div>
|     </div>
|     </div>
|     <div id="main">                                                                                                                                           [32/75]
|     <div clas
|   HTTPOptions: 
|     HTTP/1.1 200 
|     Allow: GET,HEAD,OPTIONS
|     Content-Length: 0
|     Date: Wed, 28 Dec 2022 22:47:26 GMT 
|     Connection: close
|   RTSPRequest: 
|     HTTP/1.1 505 
|     Content-Type: text/html;charset=utf-8
|     Content-Language: en
|     Content-Length: 465
|     Date: Wed, 28 Dec 2022 22:47:27 GMT 
|     <!doctype html><html lang="en"><head><title>HTTP Status 505 
|     HTTP Version Not Supported</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {fo
nt-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><b
ody><h1>HTTP Status 505                  
|_    HTTP Version Not Supported</h1></body></html>
|_http-title: My Haikus
12445/tcp open  netbios-ssn Samba smbd 4.6.2
18030/tcp open  http        Apache httpd 2.4.46 ((Unix))
|_http-title: Whack A Mole!
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.46 (Unix)
43022/tcp open  ssh         OpenSSH 8.4 (protocol 2.0)
| ssh-hostkey: 
|   3072 7bfc37b4da6ec58ea98bb780f5cd09cb (RSA)
|   256 89cdea4725d98ff894c3d65cd405bad0 (ECDSA)
|_  256 c07c6f477e94cc8bf83da0a61fa92711 (ED25519)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-serv
ice :                                    
SF-Port8080-TCP:V=7.93%I=7%D=12/28%Time=63ACC77E%P=x86_64-pc-linux-gnu%r(G
SF:etRequest,F51,"HTTP/1\.1\x20200\x20\r\nContent-Type:\x20text/html;chars
SF:et=UTF-8\r\nContent-Language:\x20en-US\r\nContent-Length:\x203762\r\nDa
SF:te:\x20Wed,\x2028\x20Dec\x202022\x2022:47:26\x20GMT\r\nConnection:\x20c
SF:lose\r\n\r\n<!DOCTYPE\x20HTML>\n<!--\n\tMinimaxing\x20by\x20HTML5\x20UP
SF:\n\thtml5up\.net\x20\|\x20@ajlkn\n\tFree\x20for\x20personal\x20and\x20c
SF:ommercial\x20use\x20under\x20the\x20CCA\x203\.0\x20license\x20\(html5up
SF:\n\thtml5up\.net\x20\|\x20@ajlkn\n\tFree\x20for\x20personal\x20and\x20c
SF:ommercial\x20use\x20under\x20the\x20CCA\x203\.0\x20license\x20\(html5up
SF:\.net/license\)\n-->\n<html>\n\t<head>\n\t\t<title>My\x20Haikus</title>
SF:\n\t\t<meta\x20charset=\"utf-8\"\x20/>\n\t\t<meta\x20name=\"viewport\"\
SF:x20content=\"width=device-width,\x20initial-scale=1,\x20user-scalable=n
SF:o\"\x20/>\n\t\t<link\x20rel=\"stylesheet\"\x20href=\"/css/main\.css\"\x
SF:20/>\n\t</head>\n\t<body>\n\t\t<div\x20id=\"page-wrapper\">\n\n\t\t\t<!
SF:--\x20Header\x20-->\n\t\t\t\n\t\t\t\t<div\x20id=\"header-wrapper\">\n\t
SF:\t\t\t\t<div\x20class=\"container\">\n\t\t\t\t\t\t<div\x20class=\"row\"
SF:>\n\t\t\t\t\t\t\t<div\x20class=\"col-12\">\n\n\t\t\t\t\t\t\t\t<header\x
SF:20id=\"header\">\n\t\t\t\t\t\t\t\t\t<h1><a\x20href=\"/\"\x20id=\"logo\"
SF:>My\x20Haikus</a></h1>\n\t\t\t\t\t\t\t\t</header>\n\n\t\t\t\t\t\t\t</di
SF:v>\n\t\t\t\t\t\t</div>\n\t\t\t\t\t</div>\n\t\t\t\t</div>\n\t\t\t\t\n\n\
SF:t\t\t\n\t\t\t\t<div\x20id=\"main\">\n\t\t\t\t\t<div\x20clas")%r(HTTPOpt
SF:ions,75,"HTTP/1\.1\x20200\x20\r\nAllow:\x20GET,HEAD,OPTIONS\r\nContent-
SF:Length:\x200\r\nDate:\x20Wed,\x2028\x20Dec\x202022\x2022:47:26\x20GMT\r
SF:\nConnection:\x20close\r\n\r\n")%r(RTSPRequest,259,"HTTP/1\.1\x20505\x2
SF:0\r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-Language:\x20e
SF:n\r\nContent-Length:\x20465\r\nDate:\x20Wed,\x2028\x20Dec\x202022\x2022
SF::47:27\x20GMT\r\n\r\n<!doctype\x20html><html\x20lang=\"en\"><head><titl
SF:e>HTTP\x20Status\x20505\x20\xe2\x80\x93\x20HTTP\x20Version\x20Not\x20Su
SF:pported</title><style\x20type=\"text/css\">body\x20{font-family:Tahoma,
SF:Arial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:white;background
SF:-color:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font-size:16px;}\
SF:x20h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\x20{color:bla
SF:ck;}\x20\.line\x20{height:1px;background-color:#525D76;border:none;}</s
SF:tyle></head><body><h1>HTTP\x20Status\x20505\x20\xe2\x80\x93\x20HTTP\x20
SF:Version\x20Not\x20Supported</h1></body></html>");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                         
# Nmap done at Wed Dec 28 23:48:34 2022 -- 1 IP address (1 host up) scanned in 75.75 seconds 
```

Now we know what services are running, let's start enumeartion with port 8080 (http).

![image](https://user-images.githubusercontent.com/87468669/211082760-0a6e30b9-4ad0-422a-8a5d-fc65c9657484.png)

Nice we can see what the web service is running.

![image](https://user-images.githubusercontent.com/87468669/211083212-891dfb88-278c-449a-ab51-7f3a092f3699.png)

clicked on the first article and checked the source page.

![image](https://user-images.githubusercontent.com/87468669/211083425-31928a85-bc75-4cb8-bb00-a3d27d93bc17.png)

We can see /api directory in the comment part of the page. let's check the api directory.

![image](https://user-images.githubusercontent.com/87468669/211083677-a9b2d34c-57fa-47d5-aab3-9f9f73401244.png)

checking the /api directory, got other directories in it. let's check the `/user/?`.

![image](https://user-images.githubusercontent.com/87468669/211083894-c60632f0-29fa-4302-adf7-f7cfc7350be7.png)

Got some creds, the description for user ademola is admin, let's try ssh using those creds.

![image](https://user-images.githubusercontent.com/87468669/211084383-cabbef84-b9de-4e37-a4de-cfd9d486b4d7.png)

Great we got ssh using those creds, let's escalate privs 




