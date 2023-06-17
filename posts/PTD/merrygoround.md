![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9003b4b7-0ea1-49a1-9061-cfc74b09da7f)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Wed Jun 14 20:47:19 2023 as: nmap -sC -sV -oN normal.txt -p 22,80,5000,8171,807,8217,8366,8383,8462,8590,8592,8964,8976 -Pn 10.150.150.100
Nmap scan report for 10.150.150.100
Host is up (0.15s latency).

PORT     STATE  SERVICE     VERSION
22/tcp   open   ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 51eb881fa426f8afb6e4fee376d201ad (RSA)
|   256 7b87fca8a82925da888e0dc63394165a (ECDSA)
|_  256 d6fa83edce58d6b369e42cecd3daed10 (ED25519)
80/tcp   open   http        Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
8072/tcp  closed unknown
5000/tcp open   http        Werkzeug httpd 1.0.1 (Python 3.6.9)
|_http-title: Index
8171/tcp closed unknown
8217/tcp closed unknown
8366/tcp closed unknown
8383/tcp closed m2mservices
8462/tcp closed unknown
8590/tcp closed unknown
8592/tcp closed unknown
8964/tcp closed unknown
8976/tcp closed unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jun 14 20:47:34 2023 -- 1 IP address (1 host up) scanned in 15.10 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/749ef83e-9072-4d4d-9e7a-5c0c2dddf860)

noticed a webserver running on port `5000`, opening it on a browser, got directed to a login page. fuzzing got nothing useful. we can see some ports that are closed but using ncat to connect to them declare part of it open. let's write a script to check what's going on.


