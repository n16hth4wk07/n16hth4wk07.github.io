## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Fri Feb 23 14:08:28 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,88,111,135,139,389,445,464,593,636,2049,3268,3269,3389,5985,9389 -Pn 192.168.194.31
Nmap scan report for 192.168.194.31
Host is up (0.14s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-23 13:12:08Z)
111/tcp  open  rpcbind       2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|_  100005  1,2,3       2049/udp6  mountd
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: Kyotosoft.com0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
2049/tcp open  mountd        1-3 (RPC #100005)
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: Kyotosoft.com0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-02-23T13:13:37+00:00; +3m32s from scanner time.
| ssl-cert: Subject: commonName=kyoto.Kyotosoft.com
| Not valid before: 2024-02-22T12:55:31
|_Not valid after:  2024-08-23T12:55:31
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Host: KYOTO; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 3m31s, deviation: 0s, median: 3m31s
| smb2-time: 
|   date: 2024-02-23T13:12:58
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Feb 23 14:10:15 2024 -- 1 IP address (1 host up) scanned in 106.98 seconds
```


## Enumerating SMB 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Kyoto]
└─$ smbclient -L //192.168.194.31/ -N               

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        dev             Disk      development & debugging share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 192.168.194.31 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
checking for anonymous share access, we got a `dev` share, 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e3375ee0-0b2e-4d01-9c1c-9051a6583e1b)

login anonymous and download the contents in the shares. 

