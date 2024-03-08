## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Fri Mar  8 01:20:45 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 21,80,135,139,443,445,3306,7680 -Pn 192.168.197.55
Nmap scan report for 192.168.197.55
Host is up (0.21s latency).

PORT     STATE  SERVICE       VERSION
21/tcp   open   ftp           FileZilla ftpd 0.9.41 beta
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
80/tcp   open   http          Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.197.55/dashboard/
135/tcp  open   msrpc         Microsoft Windows RPC
139/tcp  open   netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open   ssl/http      Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
| http-title: Welcome to XAMPP
|_Requested resource was https://192.168.197.55/dashboard/
445/tcp  open   microsoft-ds?
3306/tcp open   mysql?
| fingerprint-strings: 
|   FourOhFourRequest, HTTPOptions, JavaRMI, LANDesk-RC, LPDString, NCP, NULL, SMBProgNeg, TerminalServer, WMSRequest, giop, oracle-tns: 
|_    Host '192.168.45.248' is not allowed to connect to this MariaDB server
7680/tcp closed pando-pub
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF:01\xffj\x04Host\x20'192\.168\.45\.248'\x20is\x20not\x20allowed\x20to\x2
SF:0connect\x20to\x20this\x20MariaDB\x20server");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-03-08T00:21:56
|_  start_date: N/A
|_clock-skew: 48s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar  8 01:21:20 2024 -- 1 IP address (1 host up) scanned in 35.28 seconds
```

