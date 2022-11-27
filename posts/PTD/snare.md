![image](https://user-images.githubusercontent.com/87468669/204126974-8404620b-08e5-44cc-9fc2-7004dedf7733.png)

First we start with an nmap scan
`nmap -sC -sV -T4 -oN nmap.txt`

```
# Nmap 7.93 scan initiated Sat Nov 19 21:22:26 2022 as: nmap -sC -sV -T4 -oN quicknmap.txt -Pn 10.150.150.18
Nmap scan report for 10.150.150.18
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 2f0e73d4ae73147ec51c1584ef45a4d1 (RSA)
|_  256 390b0bc986c98eb52b0c39c763ece210 (ECDSA)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-title: Welcome to my homepage!
|_Requested resource was /index.php?page=home
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Nov 19 21:22:58 2022 -- 1 IP address (1 host up) scanned in 31.80 seconds
```

we have port 22 (ssh) and 80 (http) opened, let's enumerate http first.

![image](https://user-images.githubusercontent.com/87468669/204127066-98c6d6cf-be6c-48ea-a3a0-aff5f87e117f.png)

