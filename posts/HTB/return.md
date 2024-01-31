First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Wed Jan 31 08:59:10 2024 as: nmap -p- --min-rate 1000 -oN fulltcp.nmap -v -Pn 10.129.95.241
Nmap scan report for 10.129.95.241
Host is up (0.14s latency).
Not shown: 65506 closed tcp ports (conn-refused)
PORT      STATE    SERVICE
53/tcp    open     domain
80/tcp    open     http
88/tcp    open     kerberos-sec
135/tcp   open     msrpc
139/tcp   open     netbios-ssn
389/tcp   open     ldap
445/tcp   open     microsoft-ds
464/tcp   open     kpasswd5
593/tcp   open     http-rpc-epmap
636/tcp   open     ldapssl
3268/tcp  open     globalcatLDAP
3269/tcp  open     globalcatLDAPssl
3949/tcp  filtered drip
5985/tcp  open     wsman
9389/tcp  open     adws
47001/tcp open     winrm
49664/tcp open     unknown
49665/tcp open     unknown
49666/tcp open     unknown
49667/tcp open     unknown
49671/tcp open     unknown
49676/tcp open     unknown
49677/tcp open     unknown
49678/tcp open     unknown
49681/tcp open     unknown
49738/tcp open     unknown
61168/tcp filtered unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Wed Jan 31 09:00:30 2024 -- 1 IP address (1 host up) scanned in 80.32 seconds
```
first run nmap full port scan to know what ports are open, then  let's run default nmap scripts scan. 

```shell
# Nmap 7.94SVN scan initiated Wed Jan 31 09:09:30 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,80,88,135,139,389,445,593,636,3268,3269,3949,5985,9389,47001 -Pn 10.129.95.241
Nmap scan report for 10.129.95.241
Host is up (0.17s latency).

PORT      STATE  SERVICE       VERSION
53/tcp    open   domain        Simple DNS Plus
80/tcp    open   http          Microsoft IIS httpd 10.0
|_http-title: HTB Printer Admin Panel
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
88/tcp    open   kerberos-sec  Microsoft Windows Kerberos (server time: 2024-01-31 08:31:15Z)
135/tcp   open   msrpc         Microsoft Windows RPC
139/tcp   open   netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open   ldap          Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
445/tcp   open   microsoft-ds?
593/tcp   open   ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open   tcpwrapped
3268/tcp  open   ldap          Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
3269/tcp  open   tcpwrapped
3949/tcp  closed drip
5985/tcp  open   http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open   mc-nmf        .NET Message Framing
47001/tcp open   http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-01-31T08:31:25
|_  start_date: N/A
|_clock-skew: 21m36s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jan 31 09:10:03 2024 -- 1 IP address (1 host up) scanned in 33.25 seconds
```
now we know what services aare running on those ports. 

## Enumerating port 80 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/de2800c5-fe2d-46a5-847d-7f25e32e1587)

checking the IP addr on a browser, we get a printer admin page. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0fd2c226-23c1-405a-b500-69ff7aa61f0c)

navigating to the settings page, we got to see some ldap info. let's try fire responder and replace the domain name with our tun0 IP addr. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1a279d88-8e9b-4a38-a70d-694e6e4e4716)

get tun0 ip 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ab0e7e56-0625-4353-9fda-1754d88f150f)

fire responder 

```shell
sudo responder -I tun0
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/887088a2-3feb-4365-87e5-4d85f86d6e91)

replace the domain name with the tun0 IP addr and click update

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/133c2b3f-85a8-49f4-ae5f-4b4990709b45)

boom we got the clear text password

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f5f59216-6971-4442-a23b-2ad495e27116)

using netexec to check what service we have access to, we can see we can login to winrm.

## Initial Foothold (Winrm)

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/711d9207-ed4f-42be-ba63-49b3654e560b)

we login winrm using the creds


## Privilege Escalation 


