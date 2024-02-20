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

searching for exploit on this service, we can see there's an RCE vuln present [exploit](https://github.com/ImageMagick/ImageMagick/issues/6339)


