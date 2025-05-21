![image](https://github.com/user-attachments/assets/b18d55ca-14a8-490a-97e6-2e9231fc8eb8)

> first enum with nmap

```shell
# Nmap 7.94SVN scan initiated Wed May 21 15:09:10 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p22,53,80,88,135,139,389,445,464,593,636,3268,3269,9389 -Pn -oN 155_service.txt 10.129.90.155
Nmap scan report for 10.129.90.155
Host is up (0.21s latency).

PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH for_Windows_9.5 (protocol 2.0)
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Apache httpd 2.4.58 (OpenSSL/3.1.3 PHP/8.2.12)
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
|_http-title: Did not follow redirect to http://frizzdc.frizz.htb/home/
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-05-21 20:09:32Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Hosts: localhost, FRIZZDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-05-21T20:09:44
|_  start_date: N/A
|_clock-skew: 6h00m13s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed May 21 15:10:13 2025 -- 1 IP address (1 host up) scanned in 62.97 seconds
```

> add the `frizzdc.frizz.htb` to `/etc/hosts` file

![image](https://github.com/user-attachments/assets/414ff2af-a7ae-4950-8c1f-fc7cf91d8c99)

> checking out the web service

![image](https://github.com/user-attachments/assets/4cefd179-0489-4ae0-be5c-ca4608ff3c5f)

opening the web app on browser we can see it runs an education platform. 

![image](https://github.com/user-attachments/assets/1757a8cc-aa0f-47e6-b707-3de0ada637b7)

navigating to the staff login page, we can see it is running `gibbon v25.0.0`. 

![image](https://github.com/user-attachments/assets/6b364863-e852-4c9e-b877-091e7b60b996)

lil bit of research shows the gibbon version is vuln to lfi (local file inclusion) vulnerability.

![image](https://github.com/user-attachments/assets/ce8bae4a-fdf1-4730-b6e4-252fcaeb9f66)

we can see the endpoint for lfi is `http://frizzdc.frizz.htb/Gibbon-LMS/index.php?q=lfi`. we using gibbon.sql, and we can see the info about the sql db. 

