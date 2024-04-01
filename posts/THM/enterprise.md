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

