![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/67761934-e9e5-4486-b66a-552d42cc11b7)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Tue Jun  6 15:19:35 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 80 -Pn 10.10.10.153
Nmap scan report for 10.10.10.153
Host is up (0.39s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Blackhat highschool

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Jun  6 15:19:52 2023 -- 1 IP address (1 host up) scanned in 16.67 seconds
```
got only on port open

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1a5a1380-7d8a-47af-8e40-97233f318061)
opening the IP on a browser, we get to see it is a school web page. Let's fuzz for hidden dirs

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Teacher]
└─$ gobuster dir -u http://teacher.htb/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -x php,txt 2>/dev/null
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://teacher.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php,txt
[+] Timeout:                 10s
===============================================================
2023/06/06 15:22:09 Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 290]
/images               (Status: 301) [Size: 311] [--> http://teacher.htb/images/]
/css                  (Status: 301) [Size: 308] [--> http://teacher.htb/css/]
/manual               (Status: 301) [Size: 311] [--> http://teacher.htb/manual/]
/js                   (Status: 301) [Size: 307] [--> http://teacher.htb/js/]
/javascript           (Status: 301) [Size: 315] [--> http://teacher.htb/javascript/]
/fonts                (Status: 301) [Size: 310] [--> http://teacher.htb/fonts/]
/phpmyadmin           (Status: 403) [Size: 296]
/moodle               (Status: 301) [Size: 311] [--> http://teacher.htb/moodle/]
/.php                 (Status: 403) [Size: 290]
/server-status        (Status: 403) [Size: 299]

===============================================================
2023/06/06 19:10:25 Finished
===============================================================
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7e6f1287-2e4d-489f-b99d-dc135e60505c)

navigating to the `/image` dir, we got allot of image files.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5209c09a-200c-4465-bf0a-02863896cadf)

checking them one by one, there's a particular image that did not display.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Teacher]
└─$ curl -s -v "http://teacher.htb/images/5.png"
*   Trying 10.10.10.153:80...
* Connected to teacher.htb (10.10.10.153) port 80 (#0)
> GET /images/5.png HTTP/1.1
> Host: teacher.htb
> User-Agent: curl/7.88.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Date: Tue, 06 Jun 2023 19:12:49 GMT
< Server: Apache/2.4.25 (Debian)
< Last-Modified: Wed, 27 Jun 2018 01:43:21 GMT
< ETag: "c8-56f95bd633644"
< Accept-Ranges: bytes
< Content-Length: 200
< Content-Type: image/png
< 
Hi Servicedesk,

I forgot the last charachter of my password. The only part I remembered is Th4C00lTheacha.

Could you guys figure out what the last charachter is, or just reset it?

Thanks,
Giovanni
* Connection #0 to host teacher.htb left intact
```
using curl to check the image, we can see it is an hidden note. stating the last char of the password was forgoten 🥲. let's make a wordlist using the password available.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Teacher]
└─$ for i in {a..z}; do echo Th4C00lTheacha$i ; done > password.txt
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Teacher]
└─$ for i in {A..Z}; do echo Th4C00lTheacha$i ; done >> password.txt
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Teacher]
└─$ for i in {1..9}; do echo Th4C00lTheacha$i ; done >> password.txt
```
using a bash one liner command to  create a character to get the password last character. and manually inputing the characters `~!@#$%^&*()_+-=\":?.<>` 

```
Th4C00lTheachaa
Th4C00lTheachab
Th4C00lTheachac
Th4C00lTheachad
Th4C00lTheachae
Th4C00lTheachaf
Th4C00lTheachag
Th4C00lTheachah
Th4C00lTheachai
Th4C00lTheachaj
Th4C00lTheachak
Th4C00lTheachal
Th4C00lTheacham
Th4C00lTheacha@
Th4C00lTheacha#
Th4C00lTheacha$
Th4C00lTheacha%
Th4C00lTheacha^
Th4C00lTheacha&
Th4C00lTheacha*
Th4C00lTheacha(
Th4C00lTheacha)
Th4C00lTheacha_
Th4C00lTheacha-
Th4C00lTheacha+
Th4C00lTheacha=
Th4C00lTheachan
Th4C00lTheachao
Th4C00lTheachap
Th4C00lTheachaq
Th4C00lTheachar
Th4C00lTheachas
Th4C00lTheachat
Th4C00lTheachau
Th4C00lTheachav
Th4C00lTheachaw
Th4C00lTheachax
Th4C00lTheachay
Th4C00lTheachaz
Th4C00lTheachaA
Th4C00lTheachaB
Th4C00lTheachaC
Th4C00lTheachaD
Th4C00lTheachaE
Th4C00lTheachaF
Th4C00lTheachaG
Th4C00lTheachaH
Th4C00lTheachaI
Th4C00lTheachaJ
Th4C00lTheachaK
Th4C00lTheachaL
Th4C00lTheachaM
Th4C00lTheachaN
Th4C00lTheachaO
Th4C00lTheachaP
Th4C00lTheachaQ
Th4C00lTheachaR
Th4C00lTheachaS
Th4C00lTheachaT
Th4C00lTheachaU
Th4C00lTheachaV
Th4C00lTheachaW
Th4C00lTheachaX
Th4C00lTheachaY
Th4C00lTheachaZ
Th4C00lTheacha1
Th4C00lTheacha2
Th4C00lTheacha3
Th4C00lTheacha4
Th4C00lTheacha5
Th4C00lTheacha6
Th4C00lTheacha7
Th4C00lTheacha8
Th4C00lTheacha9
Th4C00lTheacha~
Th4C00lTheacha!
```
cool we have out password list set, let's enumerate further 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f88d92ba-594e-4bc1-ab21-58a922fb62df)

moving to the `/moodle` dir, we can see a login button. let's try to login rememer we got the username `Giovanni`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/00107a83-a4b7-4e33-a7a2-4923adae063b)

using burpsuite to bruteforce the password and we can see the password `Th4C00lTheacha#` is the correct pass. now let's login 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/369b56b4-5902-4f1c-bf85-c16ebba75d27)

Bull's eye, we are in as user `Giovanni`... let's find a way to get shell.


