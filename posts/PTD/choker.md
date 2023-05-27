![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/85688bde-6d42-4a30-921b-12355b314457)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Sat May 27 02:11:10 2023 as: nmap -sC -sV -T4 -oN quicknmap.txt -Pn 10.150.150.56
Nmap scan report for chocker.localhost (10.150.150.56)
Host is up (0.20s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f2381f67bf488d17a25c6607eaa21ca9 (RSA)
|   256 bb2df9aa436d5f8a46ba838979f8f165 (ECDSA)
|_  256 476a80eecb0e3b6c80db1b6328dc9131 (ED25519)
25/tcp  open  smtp    Postfix smtpd
|_smtp-commands: chocker.localhost, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
110/tcp open  pop3    Dovecot pop3d
|_pop3-capabilities: CAPA TOP RESP-CODES SASL(PLAIN LOGIN) PIPELINING USER AUTH-RESP-CODE UIDL
143/tcp open  imap    Dovecot imapd (Ubuntu)
|_imap-capabilities: ID LITERAL+ SASL-IR LOGIN-REFERRALS AUTH=PLAIN listed have post-login IDLE ENABLE more AUTH=LOGINA0001 capabilities Pre-login OK IMAP4rev1
Service Info: Host:  chocker.localhost; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat May 27 02:12:20 2023 -- 1 IP address (1 host up) scanned in 70.02 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/932b3c5c-f098-4b03-83a4-f17489a9ab65)

noticed a web server running on port 80, opening it on a browser, got redirected to a default apache webpage. checked the source page for sus comments, got none. did dir brute force found nothing. let's move to the next port.

### Enumerating smtp port (25)

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c1f39976-ea8c-4c35-b85d-772e5e5fe245)

found an smtp user_enum script on github [USR-25](https://github.com/SxNade/USR-25). let's run this script.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f80d6c0e-3b7b-40c4-ae21-2f8e9544ed46)

cool we got the users on smtp, let's enumerate further.





