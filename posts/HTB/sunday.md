![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2bc47b81-aad3-4fab-bb66-c2ca5921adf3)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Sun Jun  4 12:11:15 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 79,111,515,6787,22022 -Pn 10.10.10.76
Nmap scan report for 10.10.10.76                                                   
Host is up (0.17s latency).                                                        
                                                                                   
PORT      STATE SERVICE  VERSION                                                   
79/tcp    open  finger?                                                            
| fingerprint-strings:                                                             
|   GenericLines:                                                                  
|     No one logged on                                                             
|   GetRequest:                                                                    
|     Login Name TTY Idle When Where                                               
|     HTTP/1.0 ???                                                                 
|   HTTPOptions:                                                                   
|     Login Name TTY Idle When Where                                               
|     HTTP/1.0 ???                                                                                                                                                     
|     OPTIONS ???                                                                  
|   Help:                                                                          
|     Login Name TTY Idle When Where                                               
|     HELP ???                                                                     
|   RTSPRequest:                                                                   
|     Login Name TTY Idle When Where                                               
|     OPTIONS ???                                                                  
|     RTSP/1.0 ???                                                                 
|   SSLSessionReq, TerminalServerCookie:                                           
|_    Login Name TTY Idle When Where                                                                                                                                   
|_finger: No one logged on\x0D                                                     
111/tcp   open  rpcbind  2-4 (RPC #100000)                                         
515/tcp   open  printer                                                            
6787/tcp  open  ssl/http Apache httpd 2.4.33 ((Unix) OpenSSL/1.0.2o mod_wsgi/4.5.1 Python/2.7.14)
| http-title: Solaris Dashboard                                                                                                                                        
|_Requested resource was https://10.10.10.76:6787/solaris/                                                                                                             
| ssl-cert: Subject: commonName=sunday                                                                                                                                 
| Subject Alternative Name: DNS:sunday                                             
| Not valid before: 2021-12-08T19:40:00                                            
|_Not valid after:  2031-12-06T19:40:00
|_ssl-date: TLS randomness does not represent time                                 
| tls-alpn:                                                                                                                                                            
|_  http/1.1                                                                       
|_http-server-header: Apache/2.4.33 (Unix) OpenSSL/1.0.2o mod_wsgi/4.5.1 Python/2.7.14
22022/tcp open  ssh      OpenSSH 7.5 (protocol 2.0)                                
| ssh-hostkey:                                                                     
|   2048 aa0094321860a4933b87a4b6f802680e (RSA)                                    
|_  256 da2a6cfa6bb1ea161da654a10b2bee48 (ED25519)                                 
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-serv
ice :                                                                              
SF-Port79-TCP:V=7.93%I=7%D=6/4%Time=647C715C%P=x86_64-pc-linux-gnu%r(Gener         
SF:icLines,12,"No\x20one\x20logged\x20on\r\n")%r(GetRequest,93,"Login\x20\         
SF:x20\x20\x20\x20\x20\x20Name\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20         
SF:\x20\x20\x20\x20TTY\x20\x20\x20\x20\x20\x20\x20\x20\x20Idle\x20\x20\x20         
SF:\x20When\x20\x20\x20\x20Where\r\n/\x20\x20\x20\x20\x20\x20\x20\x20\x20\         
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\nGET\x20\x20\x2                                                                                             
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?         
SF:\r\nHTTP/1\.0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?         
SF:\?\?\r\n")%r(Help,5D,"Login\x20\x20\x20\x20\x20\x20\x20Name\x20\x20\x20         
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20TTY\x20\x20\x20\x20\x20         
SF:\x20\x20\x20\x20Idle\x20\x20\x20\x20When\x20\x20\x20\x20Where\r\nHELP\x         
SF:20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\         
SF:?\?\?\r\n")%r(HTTPOptions,93,"Login\x20\x20\x20\x20\x20\x20\x20Name\x20         
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20TTY\x20\x20\x20
SF:When\x20\x20\x20\x20Where\r\n/\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\         
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\nOPTIONS\x20\x20\x2         
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\nRTSP/1\.0\x2         
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\n")%r(SSL         
SF:SessionReq,5D,"Login\x20\x20\x20\x20\x20\x20\x20Name\x20\x20\x20\x20\x2         
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20TTY\x20\x20\x20\x20\x20\x20\x2         
SF:0\x20\x20Idle\x20\x20\x20\x20When\x20\x20\x20\x20Where\r\n\x16\x03\x20\         
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20         
SF:\x20\?\?\?\r\n")%r(TerminalServerCookie,5D,"Login\x20\x20\x20\x20\x20\x                                                                                             
SF:20\x20Name\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20T         
SF:TY\x20\x20\x20\x20\x20\x20\x20\x20\x20Idle\x20\x20\x20\x20When\x20\x20\         
SF:x20\x20Where\r\n\x03\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2         
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\n");                                                                                                                      
                                                                                                                                                                       
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                         
# Nmap done at Sun Jun  4 12:13:02 2023 -- 1 IP address (1 host up) scanned in 107.37 seconds
```

### Enumerating finger Port - 79 

Finger is a program you can use to find information about computer users. It usually lists the login name, the full name, and possibly other details about the user you are fingering. These details may include the office location and phone number (if known), login time, idle time, time mail was last read, and the user's plan and project files.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/613e1671-1310-4037-a9eb-bdba9d31b440)

found a finger-userenum script on github, let's check for available users 



