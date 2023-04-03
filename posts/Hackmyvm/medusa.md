First we start enumeration with nmap.

```
# Nmap 7.93 scan initiated Mon Apr  3 03:30:50 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,22,80 -Pn 192.168.0.103
Nmap scan report for 192.168.0.103
Host is up (0.00094s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 70d4efc9276f8d957aa5511951fe14dc (RSA)
|   256 3f8d243fd25ecae6c9af372347bf1d28 (ECDSA)
|_  256 0c337e4e953db02d6a5eca39910d1308 (ED25519)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.54 (Debian)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Apr  3 03:31:02 2023 -- 1 IP address (1 host up) scanned in 12.39 seconds
```

![image](https://user-images.githubusercontent.com/87468669/229398369-fb32926d-0911-443b-9ac0-d67a5f791b3c.png)

noticed a webserver running on port 80, opening it on a browser, we got directed to a default apache webpage.




