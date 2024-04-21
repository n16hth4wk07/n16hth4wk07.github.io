![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/af1c2f59-6ee3-47de-a3f8-a033491c9127)## First Enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Sun Apr 21 02:39:03 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80 -Pn 172.16.246.132
Nmap scan report for 172.16.246.132
Host is up (0.00036s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 56:9b:dd:56:a5:c1:e3:52:a8:42:46:18:5e:0c:12:86 (RSA)
|   256 1b:d2:cc:59:21:50:1b:39:19:77:1d:28:c0:be:c6:82 (ECDSA)
|_  256 9c:e7:41:b6:ad:03:ed:f5:a1:4c:cc:0a:50:79:1c:20 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Apr 21 02:39:10 2024 -- 1 IP address (1 host up) scanned in 7.01 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/933d9107-a6d5-45a3-9b3e-7900b1affaf0)

navigating the Ip on a browser, we got a page with a ping request output. 

