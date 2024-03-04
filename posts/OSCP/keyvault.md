## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Mon Mar  4 15:08:54 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80,8080 -Pn 192.168.179.207
Nmap scan report for 192.168.179.207
Host is up (0.14s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c1:99:4b:95:22:25:ed:0f:85:20:d3:63:b4:48:bb:cf (RSA)
|   256 0f:44:8b:ad:ad:95:b8:22:6a:f0:36:ac:19:d0:0e:f3 (ECDSA)
|_  256 32:e1:2a:6c:cc:7c:e6:3e:23:f4:80:8d:33:ce:9b:3a (ED25519)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: KeyVault Password Manager &amp; Vault App with Single-Sign On ...
|_http-server-header: Apache/2.4.41 (Ubuntu)
8080/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-open-proxy: Proxy might be redirecting requests
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-git: 
|   192.168.179.207:8080/.git/
|     Git repository found!
|     .gitignore matched patterns 'secret'
|     .git/config matched patterns 'key' 'user'
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|     Last commit message: Added Files and .gitignore 
|_    Project type: PHP application (guessed from .gitignore)
|_http-title: KeyVault - Password Manager
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Mar  4 15:09:07 2024 -- 1 IP address (1 host up) scanned in 13.41 seconds

```



## Enumerating port 80

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/806d1afd-a7d0-4ffd-b032-ce001c8a71f3)

checking the web server on port `80`, we can see it is running a `Key vault password manager`. 





