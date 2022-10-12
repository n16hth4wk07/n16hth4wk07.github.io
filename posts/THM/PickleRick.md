![image](https://user-images.githubusercontent.com/87468669/195387786-e9ce2227-d5d6-49f6-bd37-d49d61f7a761.png)

First we start with an nmap scan 

```
# Nmap 7.92 scan initiated Wed Oct 12 10:43:01 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 10.10.154.17
Nmap scan report for 10.10.154.17
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 55:7b:41:a6:7d:c5:62:13:c5:c2:af:8c:25:ae:24:b1 (RSA)
|   256 5c:c7:f0:af:79:69:38:f7:b0:94:00:6f:f2:de:5f:ba (ECDSA)
|_  256 e7:1b:43:9d:09:5d:17:e2:98:a9:a8:e7:e8:cf:9d:fb (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Oct 12 10:43:31 2022 -- 1 IP address (1 host up) scanned in 29.40 seconds
```

Nice we got http and ssh ports open, let's enumerate port 80. First we open the IP on our browser

![image](https://user-images.githubusercontent.com/87468669/195388802-f27a32cb-2da7-47c9-afbd-56e2a7f69dac.png)

nice we git the homepage and it's all about rick burping, asking us to logon to his computer to find the last 3 ingredients for him to change back from a pickle. Let's check source page 

![image](https://user-images.githubusercontent.com/87468669/195389823-e6676dee-3149-4c09-898d-62e9f52ba725.png)

cool we got a username: R1ckRul3s, let's burst hidden directories 

