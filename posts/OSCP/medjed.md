## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Thu Feb 29 14:56:05 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 135,139,445,3306,5040,8000,30021,33033,44330,45332,45443 -Pn 192.168.206.127
Nmap scan report for 192.168.206.127
Host is up (0.15s latency).

PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3306/tcp  open  mysql?
| fingerprint-strings: 
|   DNSVersionBindReqTCP, Help, Kerberos, NULL, RPCCheck, RTSPRequest, SMBProgNeg: 
|_    Host '192.168.45.214' is not allowed to connect to this MariaDB server
5040/tcp  open  unknown
8000/tcp  open  http-alt      BarracudaServer.com (Windows)
|_http-title: Home
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
| http-methods: 
|_  Potentially risky methods: PROPFIND PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
| http-webdav-scan: 
|   Server Date: Thu, 29 Feb 2024 13:59:38 GMT
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, PUT, COPY, DELETE, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK
|_  Server Type: BarracudaServer.com (Windows)
| fingerprint-strings: 
|   FourOhFourRequest, Socks5: 
|     HTTP/1.1 200 OK
|     Date: Thu, 29 Feb 2024 13:56:59 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   GenericLines, GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Thu, 29 Feb 2024 13:56:53 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Thu, 29 Feb 2024 13:57:05 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   RTSPRequest: 
|     HTTP/1.1 200 OK
|     Date: Thu, 29 Feb 2024 13:57:06 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   SIPOptions: 
|     HTTP/1.1 400 Bad Request
|     Date: Thu, 29 Feb 2024 13:58:14 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|     Content-Type: text/html
|     Cache-Control: no-store, no-cache, must-revalidate, max-age=0
|_    <html><body><h1>400 Bad Request</h1>Can't parse request<p>BarracudaServer.com (Windows)</p></body></html>
|_http-server-header: BarracudaServer.com (Windows)
30021/tcp open  ftp           FileZilla ftpd 0.9.41 beta
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
|_ftp-bounce: bounce working!
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -r--r--r-- 1 ftp ftp            536 Nov 03  2020 .gitignore
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 app
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 bin
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 config
| -r--r--r-- 1 ftp ftp            130 Nov 03  2020 config.ru
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 db
| -r--r--r-- 1 ftp ftp           1750 Nov 03  2020 Gemfile
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 lib
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 log
| -r--r--r-- 1 ftp ftp             66 Nov 03  2020 package.json
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 public
| -r--r--r-- 1 ftp ftp            227 Nov 03  2020 Rakefile
| -r--r--r-- 1 ftp ftp            374 Nov 03  2020 README.md
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 test
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 tmp
|_drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 vendor
33033/tcp open  unknown
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|   GetRequest, HTTPOptions: 
|     HTTP/1.0 403 Forbidden
|     Content-Type: text/html; charset=UTF-8
|     Content-Length: 3102
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8" />
|     <title>Action Controller: Exception caught</title>
|     <style>
|     body {
|     background-color: #FAFAFA;
|     color: #333;
|     margin: 0px;
|     body, p, ol, ul, td {
|     font-family: helvetica, verdana, arial, sans-serif;
|     font-size: 13px;
|     line-height: 18px;
|     font-size: 11px;
|     white-space: pre-wrap;
|     pre.box {
|     border: 1px solid #EEE;
|     padding: 10px;
|     margin: 0px;
|     width: 958px;
|     header {
|     color: #F0F0F0;
|     background: #C52F24;
|     padding: 0.5em 1.5em;
|     margin: 0.2em 0;
|     line-height: 1.1em;
|     font-size: 2em;
|     color: #C52F24;
|     line-height: 25px;
|     .details {
|_    bord
44330/tcp open  ssl/unknown
|_ssl-date: 2024-02-29T14:00:09+00:00; +40s from scanner time.
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.1 200 OK
|     Date: Thu, 29 Feb 2024 13:57:09 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Thu, 29 Feb 2024 13:57:11 GMT
|     Server: BarracudaServer.com (Windows)
|_    Connection: Close
| ssl-cert: Subject: commonName=server demo 1024 bits/organizationName=Real Time Logic/stateOrProvinceName=CA/countryName=US
| Not valid before: 2009-08-27T14:40:47
|_Not valid after:  2019-08-25T14:40:47
45332/tcp open  http          Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
|_http-title: Quiz App
| http-methods: 
|_  Potentially risky methods: TRACE
45443/tcp open  http          Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
|_http-title: Quiz App
| http-methods: 
|_  Potentially risky methods: TRACE
4 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port44330-TCP:V=7.94SVN%T=SSL%I=7%D=2/29%Time=65E08D0D%P=x86_64-pc-linu
SF:x-gnu%r(GenericLines,72,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Thu,\x2029\
SF:x20Feb\x202024\x2013:57:09\x20GMT\r\nServer:\x20BarracudaServer\.com\x2
SF:0\(Windows\)\r\nConnection:\x20Close\r\n\r\n")%r(HTTPOptions,72,"HTTP/1
SF:\.1\x20200\x20OK\r\nDate:\x20Thu,\x2029\x20Feb\x202024\x2013:57:11\x20G
SF:MT\r\nServer:\x20BarracudaServer\.com\x20\(Windows\)\r\nConnection:\x20
SF:Close\r\n\r\n");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 40s, deviation: 0s, median: 39s
| smb2-time: 
|   date: 2024-02-29T13:59:38
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Feb 29 14:59:31 2024 -- 1 IP address (1 host up) scanned in 206.16 seconds

```

## Enumerating port 8000

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6cf18313-a51c-4e79-80b2-f734910612c5)

we notice the web server on port `8000` is running barracuda drive, and we are assked to setup and administrator account. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/daabb8a6-8ce3-47a9-a871-877605a3a9ac)

setup an administrator account. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2b37aef3-baa7-4f43-ae1b-51c0285a3b8b)

hit the next button to continue. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3677ee47-17c2-4e52-8b5d-2f1666d14f2c)

remember the webapp also talked about `webdav`, so we use the username and password configured during the admin panel. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/adb52333-85b5-4b1a-aec7-ae0e130fec39)

navigate to the dir path `C/xampp/htdoc`, and uploaded a php `shell.php` cmd web shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ecad56f3-4027-453f-88b7-0ee8754d120c)

checking each web server that is running on the target, checking port `45443` we got our shell and we have RCE. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/85483277-09af-4056-b8f8-42bd0a3d302e)

we got a reverse shell 



## Privilege Escalation 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/db277185-2dc3-41fd-b12a-066d8ec5fc92)

found a password `CatastropheToes543`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c318c84e-41d2-4348-9aca-3044f46f222a)

found an interesting binary. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ef7f911b-136a-48d2-92d2-81cf952a8bb6)

query the binary info and we can modify the binary. 

```shell
C:\Users\Jerren>copy C:\Users\Jerren\shell.exe C:\bd\bd.exe                                    
copy C:\Users\Jerren\shell.exe C:\bd\bd.exe                                                    
        1 file(s) copied.                                                                      
                                                                                                                                                                                               
C:\Users\Jerren>
```
replace the binary file with a reverse shell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d4c416bb-c49c-48f4-873a-2430aee44e6d)

tried to stop and start the service, we don't have permission for that, checking user privs, we can shutdown the machine. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/80585ae9-4977-49e6-ab32-17757b797452)

restarted the machine and Boom we got reverse shell as `nt authority/system`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b1ddfc47-7e55-4f01-bb07-5f4e6d099fd3)

and we are through 🙂
