First enumeration with nmap 

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Tue Jan 10 09:53:56 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.108.220
Nmap scan report for 192.168.108.220
Host is up (0.15s latency).
Not shown: 65405 filtered tcp ports (no-response), 125 filtered tcp ports (host-unreach)
PORT      STATE  SERVICE
22/tcp    open   ssh
80/tcp    open   http
3306/tcp  open   mysql
43500/tcp open   unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Tue Jan 10 09:56:07 2023 -- 1 IP address (1 host up) scanned in 131.30 seconds
```

now we know what ports are open, let's run default nmap scripts to know what services are running on those ports.

`nmap -sC -sV -oN normal.tcp -p 22,80,3306,43500 -Pn <IP>`

```
# Nmap 7.93 scan initiated Tue Jan 10 09:58:01 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80,3306,43500 -Pn 192.168.108.220
Nmap scan report for 192.168.108.220
Host is up (0.14s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 62361a5cd3e37be170f8a3b31c4c2438 (RSA)
|   256 ee25fc236605c0c1ec47c6bb00c74f53 (ECDSA)
|_  256 835c51ac32e53a217cf6c2cd936858d8 (ED25519)
80/tcp    open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Upright
|_http-server-header: nginx/1.18.0 (Ubuntu)
3306/tcp  open  mysql   MySQL (unauthorized)
43500/tcp open  http    OpenResty web app server
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
|_http-server-header: APISIX/2.8
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Jan 10 09:58:20 2023 -- 1 IP address (1 host up) scanned in 19.61 seconds
```

cool we got to know what services are running on those ports. let's start enumeration with port 80 (http).

![image](https://user-images.githubusercontent.com/87468669/211518834-3cc7683e-6328-4925-9f4d-0440430bb26d.png)

We discover a webserver running on port 80. After opening it in a browser, we are directed to a homepage containing pictures and about. after much playing around, got nothing. let move to the next port 43500 (http).

![image](https://user-images.githubusercontent.com/87468669/211518967-9ecc2afd-2c9e-4889-9aa9-2db8fc70884b.png)

after opening the it on a browser, i got an error message `{"error_msg":"404 Route Not Found"}` back to the nmap scan result we can see the header of the web service `APISIX/2.8`. let's look for an exploit.

![image](https://user-images.githubusercontent.com/87468669/211519013-7f958518-fd53-47d5-8a26-f77208fdbd9a.png)

found an exploit on searchsploit `multiple/remote/50829.py` , so what this exploit does it injectes  malicious api keys to the header of the api and spawn a reverse shell for us.

![image](https://user-images.githubusercontent.com/87468669/211519096-92119a48-f644-4a81-9634-7f2a18aafa6b.png)

ran the exploit, fired ncat listener.

![image](https://user-images.githubusercontent.com/87468669/211519165-edcb6d28-dd34-4472-a826-39d3074849ee.png)

Boom we are in as user `franklin` stablized the shell.

## Privilege Escalation

