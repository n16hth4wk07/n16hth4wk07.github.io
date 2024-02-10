![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b80efa3b-6d37-4d1c-8644-d3ec7599a228)

first enumeration with nmap 

```shell
# Nmap 7.93 scan initiated Sat Feb 10 17:28:13 2024 as: nmap -p- --min-rate 1000 -v -Pn -oN full.tcp 10.10.230.101
Nmap scan report for ip-10-10-230-101.eu-west-1.compute.internal (10.10.230.101)
Host is up (0.00056s latency).
Not shown: 65514 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
7680/tcp  open  pando-pub
9389/tcp  open  adws
49669/tcp open  unknown
49670/tcp open  unknown
49671/tcp open  unknown
49674/tcp open  unknown
49677/tcp open  unknown
49703/tcp open  unknown
MAC Address: 02:24:96:14:7C:EB (Unknown)

Read data files from: /usr/bin/../share/nmap
# Nmap done at Sat Feb 10 17:30:33 2024 -- 1 IP address (1 host up) scanned in 140.66 seconds
```
we can see the open ports, let's run nmap default scripts scan to know what services are running on these ports

```shell
# Nmap 7.93 scan initiated Sat Feb 10 17:34:41 2024 as: nmap -sCV -T4 -p 53,88,135,139,389,445,464,593,636,3268,3269,3389,5985,7680,9389 -Pn -oN service.tcp
 10.10.230.101                 
Nmap scan report for ip-10-10-230-101.eu-west-1.compute.internal (10.10.230.101)
Host is up (0.00045s latency).                                                
                                                                              
PORT     STATE    SERVICE       VERSION    
53/tcp   open     domain        Simple DNS Plus
88/tcp   open     kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-10 17:34:49Z)
135/tcp  open     msrpc         Microsoft Windows RPC
139/tcp  open     netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open     ldap          Microsoft Windows Active Directory LDAP (Domain: thm.corp0., Site: Default-First-Site-Name)
445/tcp  open     microsoft-ds?
464/tcp  open     kpasswd5?
593/tcp  open     ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open     tcpwrapped
3268/tcp open     ldap          Microsoft Windows Active Directory LDAP (Domain: thm.corp0., Site: Default-First-Site-Name)
3269/tcp open     tcpwrapped
3389/tcp open     ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=HayStack.thm.corp
| Not valid before: 2024-01-25T21:01:31                                                                                                                     
|_Not valid after:  2024-07-26T21:01:31 
| rdp-ntlm-info:                                                                                                                                            
|   Target_Name: THM                                                                                                                                        
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: HAYSTACK
|   DNS_Domain_Name: thm.corp
|   DNS_Computer_Name: HayStack.thm.corp
|   DNS_Tree_Name: thm.corp
|   Product_Version: 10.0.17763
|_  System_Time: 2024-02-10T17:34:49+00:00
|_ssl-date: 2024-02-10T17:35:29+00:00; 0s from scanner time.
5985/tcp open     http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7680/tcp filtered pando-pub
9389/tcp open     mc-nmf        .NET Message Framing
MAC Address: 02:24:96:14:7C:EB (Unknown)
Service Info: Host: HAYSTACK; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-02-10T17:34:50
|_  start_date: N/A
|_nbstat: NetBIOS name: HAYSTACK, NetBIOS user: <unknown>, NetBIOS MAC: 022496147ceb (unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Feb 10 17:35:29 2024 -- 1 IP address (1 host up) scanned in 48.40 seconds
```
now we know what services are running on these ports

