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


## Enumerating port 80 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2fe92045-0210-4caa-8eaf-4d4b7e8bf050)

the web server running on port `80` & `443` runs xampp and fuzzing for hidden dirs gave out nothing useful.


## Enumerating SMB 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0180ced5-c745-439d-a14d-9a0d9e246ba0)

checking for smb shares, we got 2 shares on the smb server. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Shenzi]
└─$ smbclient //192.168.185.55/Shenzi/ -N
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Thu May 28 16:45:09 2020
  ..                                  D        0  Thu May 28 16:45:09 2020
  passwords.txt                       A      894  Thu May 28 16:45:09 2020
  readme_en.txt                       A     7367  Thu May 28 16:45:09 2020
  sess_klk75u2q4rpgfjs3785h6hpipp      A     3879  Thu May 28 16:45:09 2020
  why.tmp                             A      213  Thu May 28 16:45:09 2020
  xampp-control.ini                   A      178  Thu May 28 16:45:09 2020

                12941823 blocks of size 4096. 6496339 blocks available
smb: \> 
```
found some interesting files in the `/shenzi` share 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4c5451d1-5ff5-428b-9d70-6666d7a101da)

downloaded the files, and checking the `passwords.txt` file, we got a wordpress cred `admin:FeltHeadwallWight357`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8af1a08a-14bc-4ae2-aa82-9f8bf08aad7a)

given the name of the share is `shenzi` so i just include it in the webpage and boom we got a wordpress. remember we got wordpress credential. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/10b9b0ea-18cf-41fd-807c-8d8f9cc996b0)

let's login using the creds we got earlier. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2bf8da13-75ef-4c76-918b-6d07f20b1605)

logged in as admin success. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/31d566bc-86b2-495c-b7b5-f361b8f36fd5)

navigate to `theme editor`, use twenty seventeen theme. edit the 404.php and put a web shell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/01bb8b00-3038-479c-b291-ebc05a52c295)

navigate to the web shell and we got RCE. let's spawn a reverse shell

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0e36ceeb-96af-45b0-a4f8-4171c985f393)

spawn a reverse shell



## Privilege Escalation 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4f7d4400-06b7-4a5d-a40c-79d8fd5c0091)

running winpeas.exe, we can see `AlwaysInstallElevated` is enabled. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d76540df-46bb-427b-8101-ec8a4320577d)

tried it manually to confirm. we can see it is available.



