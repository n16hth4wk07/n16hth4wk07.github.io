![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8b37092c-58b1-4940-82fb-d9e892b905e5)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Mon Jun 12 16:32:51 2023 as: nmap -sC -sV -oN normal.txt -p 21,80,135,139,445,5985,47001 -Pn 10.10.10.152
Nmap scan report for 10.10.10.152
Host is up (0.16s latency).

PORT      STATE SERVICE      VERSION
21/tcp    open  ftp          Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 02-03-19  12:18AM                 1024 .rnd
| 02-25-19  10:15PM       <DIR>          inetpub
| 07-16-16  09:18AM       <DIR>          PerfLogs
| 02-25-19  10:56PM       <DIR>          Program Files
| 02-03-19  12:28AM       <DIR>          Program Files (x86)
| 02-03-19  08:08AM       <DIR>          Users
|_02-25-19  11:49PM       <DIR>          Windows
80/tcp    open  http         Indy httpd 18.1.37.13946 (Paessler PRTG bandwidth monitor)
| http-title: Welcome | PRTG Network Monitor (NETMON)
|_Requested resource was /index.htm
|_http-trane-info: Problem with XML parsing of /evox/about
|_http-server-header: PRTG/18.1.37.13946
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-06-12T15:33:11
|_  start_date: 2023-06-12T03:26:06
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Jun 12 16:33:16 2023 -- 1 IP address (1 host up) scanned in 25.45 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9b21b245-5f95-495d-a6ee-4aca2dcfa55c)

a web server running on port 80, opening it on a browser, we got directed to a web page running `PRTG Network Monitor (Netmon)`. also we can see a login page, tried default creds, no success. let's enumerate further.


### Enumerating FTP (port 21)

```
ftp> ls -al
229 Entering Extended Passive Mode (|||49882|)
125 Data connection already open; Transfer starting.
11-20-16  10:46PM       <DIR>          $RECYCLE.BIN
02-03-19  12:18AM                 1024 .rnd
11-20-16  09:59PM               389408 bootmgr
07-16-16  09:10AM                    1 BOOTNXT
02-03-19  08:05AM       <DIR>          Documents and Settings
02-25-19  10:15PM       <DIR>          inetpub
07-04-23  06:13PM            738197504 pagefile.sys
07-16-16  09:18AM       <DIR>          PerfLogs
02-25-19  10:56PM       <DIR>          Program Files
02-03-19  12:28AM       <DIR>          Program Files (x86)
12-15-21  10:40AM       <DIR>          ProgramData
02-03-19  08:05AM       <DIR>          Recovery
02-03-19  08:04AM       <DIR>          System Volume Information
02-03-19  08:08AM       <DIR>          Users
02-25-19  11:49PM       <DIR>          Windows
226 Transfer complete.
ftp>
```
anonymous login was enabled, and we got some dirs. checking online for the config file of `PRTG netmon`, it is stored in `C:\ProgramData\Paessler\PRTG Network Monitor` dir. 

![image]https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/84609e52-cc6f-41c6-b9f4-125279e26472)

on navigating to the dir, got to see some config files. downloaded the 2 files, the `.old & .bak`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f696156a-6246-46f0-9457-57cfc460768c)

checking the first file, there was nothing useful in it. checking the 2nd `.old` file, got to see a cred `prtgadmin:PrTg@dmin2018` cool let's login using this creds

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/fae12c2d-50ec-4608-b4aa-540e7760df24)

login failed. 🤔 looking at the password, we can see it ended in a year `2018`, why don't we try using another year `2019-2023`. 

```
PrTg@dmin2019
PrTg@dmin2020
PrTg@dmin2021
PrTg@dmin2022
PrTg@dmin2023
```
made a passwordlist and try bruteforce.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a912591f-77d3-414d-b24a-b406c2cb74b4)

cool we logged in using password `PrTg@dmin2019`. let's play around to pop a reverse shell. let's check online for exploits

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d3fea5d2-22a6-478e-9b4d-88624d3dcb33)

found an authenticated RCE exploit on  github. let's clone and run the exploit.

