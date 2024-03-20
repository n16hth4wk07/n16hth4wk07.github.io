## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Wed Mar 20 09:43:43 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 21,22,53,80,88,135,139,389,445,464,593,636,3268,3269,5985,9999 -Pn 10.10.29.82
Nmap scan report for 10.10.29.82
Host is up (0.16s latency).

PORT     STATE SERVICE      VERSION
21/tcp   open  ftp          Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
22/tcp   open  ssh          OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 55:15:8d:d0:54:38:1b:d6:a9:9e:3f:b0:0b:b3:14:34 (RSA)
|   256 cf:5b:e2:de:ce:3b:04:e6:8c:24:6c:2f:37:25:05:c5 (ECDSA)
|_  256 82:bf:bb:09:69:a7:25:5d:66:58:ea:c6:53:d8:c8:8e (ED25519)
53/tcp   open  domain       Simple DNS Plus
80/tcp   open  http         Microsoft IIS httpd 8.5
|_http-svn-info: ERROR: Script execution failed (use -d to debug)
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND LOCK UNLOCK PROPPATCH MKCOL PUT DELETE MOVE
|_http-server-header: Microsoft-IIS/8.5
| http-webdav-scan: 
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, LOCK, UNLOCK
|   WebDAV type: Unknown
|   Server Date: Wed, 20 Mar 2024 08:45:09 GMT
|   Server Type: Microsoft-IIS/8.5
|   Exposed Internal IPs: 
|     10.10.29.82
|   Directory Listing: 
|     http://10.10.29.82/
|     http://10.10.29.82/iis-85.png
|     http://10.10.29.82/iisstart.htm
|     http://10.10.29.82/otv.jpg
|_    http://10.10.29.82/Scarras_Super_Secret_Password.txt
|_http-title: Offline TV
88/tcp   open  kerberos-sec Microsoft Windows Kerberos (server time: 2024-03-20 08:44:58Z)
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp  open  ldap         Microsoft Windows Active Directory LDAP (Domain: kingofthe.domain, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds Windows Server 2012 R2 Standard 9600 microsoft-ds
| fingerprint-strings: 
|   SMBProgNeg: 
|_    SMBr
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap         Microsoft Windows Active Directory LDAP (Domain: kingofthe.domain, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9999/tcp open  http         Microsoft IIS httpd 8.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/plain).
|_http-server-header: Microsoft-IIS/8.5
Service Info: Host: OFFLINE; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: OFFLINE, NetBIOS user: <unknown>, NetBIOS MAC: 02:62:df:0b:6d:5b (unknown)
| smb2-security-mode: 
|   3:0:2: 
|_    Message signing enabled and required
| smb-os-discovery: 
|   OS: Windows Server 2012 R2 Standard 9600 (Windows Server 2012 R2 Standard 6.3)
|   OS CPE: cpe:/o:microsoft:windows_server_2012::-
|   Computer name: Offline
|   NetBIOS computer name: 
|   Domain name: kingofthe.domain
|   Forest name: kingofthe.domain
|   FQDN: Offline.kingofthe.domain
|_  System time: 2024-03-20T01:45:10-07:00
| smb2-time: 
|   date: 2024-03-20T08:45:09
|_  start_date: 2024-03-20T08:39:50
|_clock-skew: mean: 2h21m08s, deviation: 4h02m30s, median: 1m07s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Mar 20 09:44:15 2024 -- 1 IP address (1 host up) scanned in 31.50 seconds

```


## Enumerating web server

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c650535e-d534-4d76-beb8-0611873cb09d)

there's a web server running on port `80`, opening it on a browser, we got a webpage.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8fecdc57-1212-4fa9-b8fe-e4800aeb244b)

viewing the page source, we got a comment potential password. `OfflineTV2020`. 


## Enumerating SMB 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Offline]
└─$ sudo nxc smb 10.10.29.82 -u anonymous -p '' --rid-brute
SMB         10.10.29.82     445    OFFLINE          [*] Windows Server 2012 R2 Standard 9600 x64 (name:OFFLINE) (domain:kingofthe.domain) (signing:True) (SMBv1:True)
SMB         10.10.29.82     445    OFFLINE          [+] kingofthe.domain\anonymous: 
SMB         10.10.29.82     445    OFFLINE          498: KingOfTheDomain\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          500: KingOfTheDomain\Administrator (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          501: KingOfTheDomain\Guest (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          502: KingOfTheDomain\krbtgt (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          512: KingOfTheDomain\Domain Admins (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          513: KingOfTheDomain\Domain Users (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          514: KingOfTheDomain\Domain Guests (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          515: KingOfTheDomain\Domain Computers (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          516: KingOfTheDomain\Domain Controllers (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          517: KingOfTheDomain\Cert Publishers (SidTypeAlias)
SMB         10.10.29.82     445    OFFLINE          518: KingOfTheDomain\Schema Admins (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          519: KingOfTheDomain\Enterprise Admins (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          520: KingOfTheDomain\Group Policy Creator Owners (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          521: KingOfTheDomain\Read-only Domain Controllers (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          522: KingOfTheDomain\Cloneable Domain Controllers (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          525: KingOfTheDomain\Protected Users (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          553: KingOfTheDomain\RAS and IAS Servers (SidTypeAlias)
SMB         10.10.29.82     445    OFFLINE          571: KingOfTheDomain\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         10.10.29.82     445    OFFLINE          572: KingOfTheDomain\Denied RODC Password Replication Group (SidTypeAlias)
SMB         10.10.29.82     445    OFFLINE          1000: KingOfTheDomain\WinRMRemoteWMIUsers__ (SidTypeAlias)
SMB         10.10.29.82     445    OFFLINE          1001: KingOfTheDomain\OFFLINE$ (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          1102: KingOfTheDomain\DnsAdmins (SidTypeAlias)
SMB         10.10.29.82     445    OFFLINE          1103: KingOfTheDomain\DnsUpdateProxy (SidTypeGroup)
SMB         10.10.29.82     445    OFFLINE          1106: KingOfTheDomain\mykull (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          1107: KingOfTheDomain\poki (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          1108: KingOfTheDomain\lily (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          1109: KingOfTheDomain\toast (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          1110: KingOfTheDomain\scarra (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          1111: KingOfTheDomain\yvonne (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          1112: KingOfTheDomain\fed (SidTypeUser)
SMB         10.10.29.82     445    OFFLINE          1113: KingOfTheDomain\SVC_ROBOTARMY (SidTypeUser)
```
using cme to do `rid-brute` we got lists of users in the domain 


