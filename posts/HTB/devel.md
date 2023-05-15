First enumeration with nmap 

`nmap -sC -sV -T4 -oN normal.txt -p 21,80 -Pn 10.10.10.5`

```
# Nmap 7.93 scan initiated Mon May 15 23:12:02 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,80 -Pn 10.10.10.5
Nmap scan report for 10.10.10.5
Host is up (0.14s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  02:06AM       <DIR>          aspnet_client
| 03-17-17  05:37PM                  689 iisstart.htm
|_03-17-17  05:37PM               184946 welcome.png
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp open  http    Microsoft IIS httpd 7.5
|_http-server-header: Microsoft-IIS/7.5
|_http-title: IIS7
| http-methods: 
|_  Potentially risky methods: TRACE
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon May 15 23:12:18 2023 -- 1 IP address (1 host up) scanned in 15.62 seconds
```
now we know what port and service running, let's enumerate port 21 first.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/devel]
└─$ ftp 10.10.10.5
Connected to 10.10.10.5.
220 Microsoft FTP Service
Name (10.10.10.5:n16hth4wk): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
229 Entering Extended Passive Mode (|||49157|)
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          aspnet_client
03-17-17  05:37PM                  689 iisstart.htm
03-17-17  05:37PM               184946 welcome.png
226 Transfer complete.
ftp> put normal.txt 
local: normal.txt remote: normal.txt
229 Entering Extended Passive Mode (|||49158|)
125 Data connection already open; Transfer starting.
100% |**************************************************************************************************************************|   914        5.58 MiB/s    --:-- ETA
226 Transfer complete.
914 bytes sent in 00:00 (5.30 KiB/s)
ftp> 
```
we have anonymous login enabled and there's a file upload availble. uploaded `normal.txt` file to see if we can access it through the web.

