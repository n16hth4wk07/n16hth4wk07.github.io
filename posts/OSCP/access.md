## First eunumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Mon Feb 26 10:52:11 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,80,88,135,139,389,445,464,593,636,3268,3269,5985,9389 -Pn 192.168.163.187
Nmap scan report for 192.168.163.187
Host is up (0.15s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-title: Access The Event
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-26 09:52:56Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Host: SERVER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-02-26T09:53:10
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 36s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Feb 26 10:53:17 2024 -- 1 IP address (1 host up) scanned in 65.29 seconds
```


## Enumerating port 80 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/82904320-6246-447e-8f83-13db9f8822ca)

there's a web server running on port port `80`. checking the ip on a browser, we got a webpage 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/483f2902-3973-49ff-9eb4-2b745167abfd)

fuzzing the web application, we got diff dirs.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/37803a93-f0eb-4210-b80f-f485d848e830)

There's an upload function in the `buy-ticket` let's try upload a malicious file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bd416e86-70c2-43b2-b7f5-f301e9d235a6)

trying to upload a php shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/400c15ea-ba97-4613-babb-8fda69f695fc)

we got error file type not supported. let's try bypassing it

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/beaa8b57-e876-4687-9443-3a7a5016296b)

after some research, found out we could overwrite the `.htaccess` and make a php shell uploadable. 

```
echo "AddType application/x-httpd-php .php16" > .htaccess
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bcfe0764-5268-457b-831f-0fbbd8dc3e04)

upload a file with `<?php phpinfo(); ?>` as `.php16`. 




