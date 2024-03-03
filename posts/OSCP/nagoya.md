## First enumeration with nmap

```shell
# Nmap 7.94SVN scan initiated Sun Mar  3 21:23:15 2024 as: nmap -sC -sV -T4 -oN normal.txt -p 53,80,88,135,139,389,445,636,3268,3269,3389,5985,9389 -Pn 192.168.211.21
Nmap scan report for 192.168.211.21
Host is up (0.61s latency).

PORT     STATE SERVICE           VERSION
53/tcp   open  domain            Simple DNS Plus
80/tcp   open  http              Microsoft IIS httpd 10.0
|_http-title: Nagoya Industries - Nagoya
|_http-server-header: Microsoft-IIS/10.0
88/tcp   open  kerberos-sec      Microsoft Windows Kerberos (server time: 2024-03-03 20:24:09Z)
135/tcp  open  msrpc             Microsoft Windows RPC
139/tcp  open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: nagoya-industries.com0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
636/tcp  open  ldapssl?
3268/tcp open  ldap              Microsoft Windows Active Directory LDAP (Domain: nagoya-industries.com0., Site: Default-First-Site-Name)
3269/tcp open  globalcatLDAPssl?
3389/tcp open  ms-wbt-server     Microsoft Terminal Services
| ssl-cert: Subject: commonName=nagoya.nagoya-industries.com
| Not valid before: 2024-03-02T20:18:17
|_Not valid after:  2024-09-01T20:18:17
|_ssl-date: 2024-03-03T20:25:17+00:00; +44s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: NAGOYA-IND
|   NetBIOS_Domain_Name: NAGOYA-IND
|   NetBIOS_Computer_Name: NAGOYA
|   DNS_Domain_Name: nagoya-industries.com
|   DNS_Computer_Name: nagoya.nagoya-industries.com
|   DNS_Tree_Name: nagoya-industries.com
|   Product_Version: 10.0.17763
|_  System_Time: 2024-03-03T20:24:38+00:00
5985/tcp open  http              Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp open  mc-nmf            .NET Message Framing
Service Info: Host: NAGOYA; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 44s, deviation: 0s, median: 43s
| smb2-time: 
|   date: 2024-03-03T20:24:38
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Mar  3 21:24:41 2024 -- 1 IP address (1 host up) scanned in 85.84 seconds
```




