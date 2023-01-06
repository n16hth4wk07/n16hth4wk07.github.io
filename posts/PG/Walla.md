First Enumeration With nmap 

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn  <IP>`

```
# Nmap 7.93 scan initiated Fri Jan  6 08:07:43 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.79.97
Increasing send delay for 192.168.79.97 from 0 to 5 due to 214 out of 712 dropped probes since last increase.
Increasing send delay for 192.168.79.97 from 5 to 10 due to 46 out of 152 dropped probes since last increase.
Increasing send delay for 192.168.79.97 from 10 to 20 due to 14 out of 45 dropped probes since last increase.
Warning: 192.168.79.97 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.79.97
Host is up (0.16s latency).
Not shown: 39026 filtered tcp ports (no-response), 26504 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
8091/tcp open  jamlink

Read data files from: /usr/bin/../share/nmap
# Nmap done at Fri Jan  6 08:09:15 2023 -- 1 IP address (1 host up) scanned in 92.33 seconds
```

now we know what ports are open, let run nmap default scripts to know what services ar running on those ports.

`nmap -sC -sV -oN normal.tcp -p 23,25,22,53,8091 -Pn <IP>`

```
# Nmap 7.93 scan initiated Fri Jan  6 08:10:40 2023 as: nmap -sC -sV -oN normal.tcp -p 23,25,22,53,8091 -Pn 192.168.79.97
Nmap scan report for 192.168.79.97
Host is up (0.19s latency).

PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 02715dc8b943ba6ac8ed15c56cb2f5f9 (RSA)
|   256 f3e510d416a99e034738baac18245328 (ECDSA)
|_  256 024f99ec856d794388b2b57cf091fe74 (ED25519)
23/tcp   open  telnet     Linux telnetd
25/tcp   open  smtp       Postfix smtpd
|_smtp-commands: walla, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
53/tcp   open  tcpwrapped
8091/tcp open  http       lighttpd 1.4.53
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=RaspAP
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: lighttpd/1.4.53
Service Info: Host:  walla; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jan  6 08:11:04 2023 -- 1 IP address (1 host up) scanned in 24.49 seconds
```

Now we know what services are running on those port, first let's enumerate port 8091.

