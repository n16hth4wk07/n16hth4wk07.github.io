First enumeration with nmap 

```
# Nmap 7.93 scan initiated Thu May 25 15:29:35 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80 -Pn 192.168.204.218
Nmap scan report for 192.168.204.218
Host is up (0.14s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 deb52389bb9fd41ab50453d0b75cb03f (RSA)
|   256 160914eab9fa17e945395e3bb4fd110a (ECDSA)
|_  256 9f665e71b9125ded705a4f5a8d0d65d5 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
| http-title:             Monitorr            | Monitorr        
|_Requested resource was http://192.168.204.218/mon/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu May 25 15:29:51 2023 -- 1 IP address (1 host up) scanned in 15.69 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/297fa813-0310-484a-b3ff-a10485490f9f)

a web server was running on port 80, opning it on a browser, got redirected to a webpage running monitoring service. below we can see the version of this service `1.7.6m`. let's chck for exploits.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/38d147c1-c4b5-4f39-b512-a9cace4658ff)

found an RCE exploit on searchsploit. download the exploit.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/948b4c61-8c07-4128-97e0-e023dfbb1489)

ran the exploit and bingo we got a reverse shell.


## Privilege Escalation



