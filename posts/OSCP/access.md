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



