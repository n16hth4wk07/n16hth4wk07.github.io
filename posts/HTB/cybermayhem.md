## First enumerationg with nmap 

```shell
# Nmap 7.94SVN scan initiated Wed Mar 13 14:27:57 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 21,22,80,8443,10251 -Pn 10.10.111.102
Nmap scan report for 10.10.111.102
Host is up (0.21s latency).

PORT      STATE  SERVICE   VERSION
21/tcp    closed ftp
22/tcp    open   ssh       OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp    open   http      Apache httpd 2.4.41 ((Ubuntu))
| http-title: elFinder 2.x source version with PHP connector
|_Requested resource was elfinder.src.html
|_http-server-header: Apache/2.4.41 (Ubuntu)
8443/tcp  closed https-alt
10251/tcp closed unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Mar 13 14:28:11 2024 -- 1 IP address (1 host up) scanned in 14.63 seconds
```

