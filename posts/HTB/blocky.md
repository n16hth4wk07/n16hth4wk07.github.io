![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1bbc414d-6e9b-4af8-b042-3de09db132ee)

first enumeration with nmap

![[Pasted image 20230602000453.png]]first enumeration with nmap 

```
# Nmap 7.93 scan initiated Thu Jun  1 23:35:37 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,80,22,8192,25565 -Pn 10.10.10.37
Nmap scan report for blocky.htb (10.10.10.37)
Host is up (0.13s latency).

PORT      STATE  SERVICE   VERSION
21/tcp    open   ftp       ProFTPD 1.3.5a
22/tcp    open   ssh       OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d62b99b4d5e753ce2bfcb5d79d79fba2 (RSA)
|   256 5d7f389570c9beac67a01e86e7978403 (ECDSA)
|_  256 09d5c204951a90ef87562597df837067 (ED25519)
80/tcp    open   http      Apache httpd 2.4.18
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-generator: WordPress 4.8
|_http-title: BlockyCraft &#8211; Under Construction!
8192/tcp  closed sophos
25565/tcp open   minecraft Minecraft 1.11.2 (Protocol: 127, Message: A Minecraft Server, Users: 0/20)
Service Info: Host: 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Jun  1 23:35:54 2023 -- 1 IP address (1 host up) scanned in 17.19 seconds
```
add `blocky.htb` to `/etc/hosts` file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/195696e6-0cec-4e17-beef-cd52712acaa0)

open blocky.htb on browser and see is running a wordpress service. let's use wpscan to check for any plugins or username.


