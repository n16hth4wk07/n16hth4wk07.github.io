## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Thu Mar 21 23:35:44 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80,8338 -Pn 192.168.190.32
Nmap scan report for 192.168.190.32
Host is up (0.19s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
|_  256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)
80/tcp   open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.52 (Ubuntu)
8338/tcp open  unknown
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Server: Maltrail/0.52
|     Date: Thu, 21 Mar 2024 22:37:03 GMT
|     Connection: close
|     Content-Type: text/html
|     Last-Modified: Sat, 31 Dec 2022 22:58:57 GMT
|     Content-Security-Policy: default-src 'self'; style-src 'self' 'unsafe-inline'; img-src * blob:; script-src 'self' 'unsafe-eval' https://stat.ripe.net; frame-src *; object-src 'none'; block-all-mixed-content;
|     Cache-Control: no-cache
|     Content-Length: 7091
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta http-equiv="X-UA-Compatible" content="IE=edge">
|     <meta http-equiv="Content-Type" content="text/html;charset=utf8">
|     <meta name="viewport" content="width=device-width, user-scalable=no">
|     <meta name="robots" content="noindex, nofollow">
|     <title>Maltrail</title>
|     <link rel="stylesheet" type="text/css" href="css/thirdparty.min.css">
|     <link rel="stylesheet" type="text/css" hre
|   HTTPOptions: 
|     HTTP/1.0 501 Unsupported method ('OPTIONS')
|     Server: Maltrail/0.52
|     Date: Thu, 21 Mar 2024 22:37:03 GMT
|     Connection: close
|     Content-Type: text/html;charset=utf-8
|     Content-Length: 500
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
|     "http://www.w3.org/TR/html4/strict.dtd">
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 501</p>
|     <p>Message: Unsupported method ('OPTIONS').</p>
|     <p>Error code explanation: HTTPStatus.NOT_IMPLEMENTED - Server does not support this operation.</p>
|     </body>
|_    </html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF:upport\x20this\x20operation\.</p>\n\x20\x20\x20\x20</body>\n</html>\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Mar 21 23:37:28 2024 -- 1 IP address (1 host up) scanned in 103.76 seconds

```


## Enumerating web server 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/504ed636-805c-47bf-a402-cee1ce390089)

there's a web server running on port `80`, opening it on a browser w got a default apache webpage. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a5b0f060-8cd1-41d4-a6cd-2801c055ecf2)

fuzzing the wweb url, got nothing hidden. 


## Enumerating port 8338 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/73ded4e9-de1b-43b4-bec0-e5e2db6369bc)

we can see it is running `mailtrail 0.52`. let's look for exploit. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/99045b74-3133-42fc-8880-339940e9fc6f)

found and exploit on github. [exploit](https://github.com/spookier/Maltrail-v0.53-Exploit). 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9cb0ef34-2b62-4505-be6e-86ebcd7f34ae)

run the exploit and we got a reverse shell. 



## Privilege Escalation


