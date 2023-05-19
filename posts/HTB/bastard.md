First enumeration with nmap 

```
# Nmap 7.93 scan initiated Tue May 16 23:11:48 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 80,135 -Pn 10.10.10.9
Nmap scan report for 10.10.10.9
Host is up (0.15s latency).

PORT    STATE SERVICE VERSION
80/tcp  open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Welcome to Bastard | Bastard
| http-robots.txt: 36 disallowed entries (15 shown)
| /includes/ /misc/ /modules/ /profiles/ /scripts/ 
| /themes/ /CHANGELOG.txt /cron.php /INSTALL.mysql.txt 
| /INSTALL.pgsql.txt /INSTALL.sqlite.txt /install.php /INSTALL.txt 
|_/LICENSE.txt /MAINTAINERS.txt
|_http-server-header: Microsoft-IIS/7.5
|_http-generator: Drupal 7 (http://drupal.org)
135/tcp open  msrpc   Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/
```
cool we know what ports and services are running.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b912d9b3-96ce-46d3-9808-70b68a07beb1)

noticed a web server running on port 80, opening it on a browser we can see it is running a drupal cms. let's check for exploit online.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0d122d82-2772-41f2-a561-586f003a7556)

found an RCE exloit on github... [exploit](https://github.com/pimps/CVE-2018-7600). cloned the repo, let's run the exploit:

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bfd170b7-f083-44c1-90a2-b3a943a31de8)

cool we got RCE, let's pop a reverse shell. fire up ncat listener

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ef23fd5e-fee9-4499-b6bc-f1a08bacad61)

running a powershell base64 reverse shell payload, check our ncat listener.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1ded4814-e7d4-4871-847f-11531a10e5c4)

Boom got a reverse shell.


## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1e825f38-9614-46ff-98ab-a09507f8889a)

first check `systeminfo`, copy the output of systeminfo into a file and save it

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/12746730-473e-4044-beff-10f1c95f13ff)

ran windows-exploit-suggester found [here](https://github.com/AonCyberLabs/Windows-Exploit-Suggester). we can see so many kernel exploit we can use. let's use `MS10-059`. which you can find [windows-exploit](https://github.com/ASR511-OO7/windows-kernel-exploits/tree/master/MS10-059).

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b45d36f7-d1fc-4e37-af71-dba22159e491)

Download the exe file, and send to the target.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9a9dbb4a-3331-4b43-ae0a-51a6cdc9ae10)

ran the exploit and boom got a reverse shell as `nt authority\system`


