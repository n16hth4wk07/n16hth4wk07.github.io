![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b00d3e11-1b23-480e-9cf4-e84c94d32e0b)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Sat Jun  3 04:41:21 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,23,80 -Pn 10.10.10.98
Nmap scan report for access.htb (10.10.10.98)
Host is up (0.16s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV failed: 425 Cannot open data connection.
| ftp-syst: 
|_  SYST: Windows_NT
23/tcp open  telnet?
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: MegaCorp
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jun  3 04:44:29 2023 -- 1 IP address (1 host up) scanned in 187.77 seconds
```


### Enumerating FTP 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Access]
└─$ ftp access.htb                                                                                                                                                     Connected to access.htb.                                                           
220 Microsoft FTP Service                                                                                                                                              
Name (access.htb:n16hth4wk): anonymous                                             
331 Anonymous access allowed, send identity (e-mail name) as password.
Password:                               
230 User logged in.
Remote system type is Windows_NT.
ftp> ls -al
425 Cannot open data connection.
200 PORT command successful.                                                       
125 Data connection already open; Transfer starting.
08-23-18  09:16PM       <DIR>          Backups 
08-24-18  10:00PM       <DIR>          Engineer
226 Transfer complete.
ftp> cd Backups            
250 CWD command successful.
ftp> ls -al                 
200 PORT command successful.                                                       
125 Data connection already open; Transfer starting.     
08-23-18  09:16PM              5652480 backup.mdb
226 Transfer complete.        
ftp> mget backup.mdb               
mget backup.mdb [anpqy?]? y 
200 PORT command successful.                                                       
125 Data connection already open; Transfer starting.                                                                                                                          |    -1        0.00 KiB/s    --:-- ETA
550 The specified network name is no longer available. 
WARNING! 87 bare linefeeds received in ASCII mode.
ftp> cd ../
250 CWD command successful.
ftp> ls -al
200 PORT command successful.
125 Data connection already open; Transfer starting.
08-23-18  09:16PM       <DIR>          Backups
08-24-18  10:00PM       <DIR>          Engineer
226 Transfer complete.
ftp> cd Engineer
250 CWD command successful.
ftp> ls -al
200 PORT command successful.
125 Data connection already open; Transfer starting.
08-24-18  01:16AM                10870 Access Control.zip
226 Transfer complete.
ftp> mget "Access Control.zip"
mget Access Control.zip [anpqy?]? y
200 PORT command successful.
125 Data connection already open; Transfer starting.
100% |**************************************************************************************************************************| 10870       22.92 KiB/s    00:00 ETA
226 Transfer complete.
WARNING! 45 bare linefeeds received in ASCII mode.
File may not have transferred correctly.
10870 bytes received in 00:00 (22.84 KiB/s)
ftp> 
```
