## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Sun Apr 14 01:17:19 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,8080 -Pn 192.168.215.246
Nmap scan report for 192.168.215.246
Host is up (0.13s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 98:4e:5d:e1:e6:97:29:6f:d9:e0:d4:82:a8:f6:4f:3f (RSA)
|   256 57:23:57:1f:fd:77:06:be:25:66:61:14:6d:ae:5e:98 (ECDSA)
|_  256 c7:9b:aa:d5:a6:33:35:91:34:1e:ef:cf:61:a8:30:1c (ED25519)
8080/tcp open  http    WSGIServer 0.2 (Python 3.8.10)
|_http-title: Boost Your Day!
|_http-server-header: WSGIServer/0.2 CPython/3.8.10
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Apr 14 01:17:31 2024 -- 1 IP address (1 host up) scanned in 11.59 seconds
```

## Enumerating Web

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6b8a5cfc-a572-463c-be2f-6cb819211a0f)

first open it on a browser, got a web page talking bout elon musk and othere stuffs. 

