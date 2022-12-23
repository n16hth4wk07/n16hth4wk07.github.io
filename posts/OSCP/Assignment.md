First we start with an nmap scan 

`nmap -sC -sV -T4 -oN nmap.txt <IP>`

```
# Nmap 7.93 scan initiated Thu Dec 22 19:46:08 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 192.168.145.224                                                         
Warning: 192.168.145.224 giving up on port because retransmission cap hit (6).                                                                                        
Nmap scan report for 192.168.145.224                                                                                                                                  
Host is up (0.19s latency).                                                                                                                                           
Not shown: 994 closed tcp ports (conn-refused)                                                                                                                        
PORT     STATE    SERVICE   VERSION                                                                                                                                   
22/tcp   open     ssh       OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)                                                                              
| ssh-hostkey:                                                                                                                                                        
|   3072 62361a5cd3e37be170f8a3b31c4c2438 (RSA)                                                                                                                       
|   256 ee25fc236605c0c1ec47c6bb00c74f53 (ECDSA)                                                                                                                      
|_  256 835c51ac32e53a217cf6c2cd936858d8 (ED25519)                                                                                                                    
80/tcp   open     http                                                                                                                                                
| fingerprint-strings:                                                                                                                                                
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, Help, JavaRMI, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NCP, NotesRPC, RPCCheck, RTSP
Request, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, WMSRequest, X11Probe, afp, giop, ms-sql-s, oracle-tns:           
|     HTTP/1.1 400 Bad Request                                                                                                                                        
|   FourOhFourRequest, GetRequest, HTTPOptions:                                                                                                                       
|     HTTP/1.0 403 Forbidden                                                                                                                                          
|     Content-Type: text/html; charset=UTF-8                                                                                                                          
|_    Content-Length: 0                                                                                                                                               
|_http-title: notes.pg                                                                                                                                                
1296/tcp filtered dproxy                                                                                                                                              
4445/tcp filtered upnotifyp                                                                                                                                           
6666/tcp filtered irc                                                                                                                                                 
8000/tcp open     http-alt                                                                                                                                            
| fingerprint-strings:                                                                                                                                                
|   FourOhFourRequest:                                                                                                                                                
|     HTTP/1.0 404 Not Found                                                                                                                                          
|     Content-Type: text/html; charset=UTF-8                                                                                                                          
|     Set-Cookie: lang=en-US; Path=/; Max-Age=2147483647                                                                                                              
|     Set-Cookie: i_like_gogs=6f281cd94540c4c7; Path=/; HttpOnly                                                                                                      
|     Set-Cookie: _csrf=OKv2gZc31tdlL7qsvpPgcFJ8TSs6MTY3MTczNDgyMzE5ODg4NDMxNA; Path=/; Domain=assignment.pg; Expires=Fri, 23 Dec 2022 18:47:03 GMT; HttpOnly         
|     X-Content-Type-Options: nosniff                                                                                                                                 
|     X-Frame-Options: DENY                                                                                                                                           
|     Date: Thu, 22 Dec 2022 18:47:03 GMT                                                                                                                             
|     <!DOCTYPE html>                                                                                                                                                 
|     <html>                                                                                                                                                          
|     <head data-suburl=""> 
|     <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
|     <meta http-equiv="X-UA-Compatible" content="IE=edge"/>
|     <meta name="author" content="Gogs" />
|     <meta name="description" content="Gogs is a painless self-hosted Git service" />
|     <meta name="keywords" content="go, git, self-hosted, gogs">
|     <meta name="referrer" content="no-referrer" />
|     <meta name="_csrf" content="OKv2gZc31tdlL7qsvpPgcFJ8TSs6MTY3MTczND
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Content-Type: text/html; charset=UTF-8
|     Set-Cookie: lang=en-US; Path=/; Max-Age=2147483647
|     Set-Cookie: i_like_gogs=9b98f2d30362da7c; Path=/; HttpOnly
|     Set-Cookie: _csrf=IiAHGWuYSbIdjDQGwaA5sRUJ-gk6MTY3MTczNDgxNzU4MDYwMzYwOA; Path=/; Domain=assignment.pg; Expires=Fri, 23 Dec 2022 18:46:57 GMT; HttpOnly
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: DENY
|     Date: Thu, 22 Dec 2022 18:46:57 GMT 
|     <!DOCTYPE html>
|     <html>
|     <head data-suburl="">
|     <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
|     <meta http-equiv="X-UA-Compatible" content="IE=edge"/>
|     <meta name="author" content="Gogs" />
|     <meta name="description" content="Gogs is a painless self-hosted Git service" />
|     <meta name="keywords" content="go, git, self-hosted, gogs">
|     <meta name="referrer" content="no-referrer" />
|_    <meta name="_csrf" content="IiAHGWuYSbIdjDQGwaA5sRUJ-gk6MTY3MTczNDgxNzU4M
|_http-title: Gogs
|_http-open-proxy: Proxy might be redirecting requests
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-s
ervice :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port80-TCP:V=7.93%I=7%D=12/22%Time=63A4A621%P=x86_64-pc-linux-gnu%r(Get
SF:Request,55,"HTTP/1\.0\x20403\x20Forbidden\r\nContent-Type:\x20text/html
SF:;\x20charset=UTF-8\r\nContent-Length:\x200\r\n\r\n")%r(HTTPOptions,55,"
SF:HTTP/1\.0\x20403\x20Forbidden\r\nContent-Type:\x20text/html;\x20charset                                                                                    [32/112]
SF:=UTF-8\r\nContent-Length:\x200\r\n\r\n")%r(RTSPRequest,1C,"HTTP/1\.1\x2
SF:0400\x20Bad\x20Request\r\n\r\n")%r(X11Probe,1C,"HTTP/1\.1\x20400\x20Bad
SF:\x20Request\r\n\r\n")%r(FourOhFourRequest,55,"HTTP/1\.0\x20403\x20Forbi
SF:dden\r\nContent-Type:\x20text/html;\x20charset=UTF-8\r\nContent-Length:
SF:\x200\r\n\r\n")%r(GenericLines,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r
SF:\n\r\n")%r(RPCCheck,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(
SF:DNSVersionBindReqTCP,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r
SF:(DNSStatusRequestTCP,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r
SF:(Help,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(SSLSessionReq,
SF:1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(TerminalServerCookie
SF:,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(TLSSessionReq,1C,"H
SF:TTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(Kerberos,1C,"HTTP/1\.1\x2
SF:0400\x20Bad\x20Request\r\n\r\n")%r(SMBProgNeg,1C,"HTTP/1\.1\x20400\x20B
SF:ad\x20Request\r\n\r\n")%r(LPDString,1C,"HTTP/1\.1\x20400\x20Bad\x20Requ
SF:est\r\n\r\n")%r(LDAPSearchReq,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\
SF:n\r\n")%r(LDAPBindReq,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%
SF:r(SIPOptions,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(LANDesk
SF:-RC,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(TerminalServer,1
SF:C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(NCP,1C,"HTTP/1\.1\x20
SF:400\x20Bad\x20Request\r\n\r\n")%r(NotesRPC,1C,"HTTP/1\.1\x20400\x20Bad\
SF:x20Request\r\n\r\n")%r(JavaRMI,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r
SF:\n\r\n")%r(WMSRequest,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%
SF:r(oracle-tns,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(ms-sql-
SF:s,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(afp,1C,"HTTP/1\.1\
SF:x20400\x20Bad\x20Request\r\n\r\n")%r(giop,1C,"HTTP/1\.1\x20400\x20Bad\x
SF:20Request\r\n\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port8000-TCP:V=7.93%I=7%D=12/22%Time=63A4A621%P=x86_64-pc-linux-gnu%r(G
SF:enericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20
SF:text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\
SF:x20Request")%r(GetRequest,206A,"HTTP/1\.0\x20200\x20OK\r\nContent-Type:
SF:\x20text/html;\x20charset=UTF-8\r\nSet-Cookie:\x20lang=en-US;\x20Path=/
SF:;\x20Max-Age=2147483647\r\nSet-Cookie:\x20i_like_gogs=9b98f2d30362da7c;
SF:\x20Path=/;\x20HttpOnly\r\nSet-Cookie:\x20_csrf=IiAHGWuYSbIdjDQGwaA5sRU
SF:J-gk6MTY3MTczNDgxNzU4MDYwMzYwOA;\x20Path=/;\x20Domain=assignment\.pg;\x
SF:20Expires=Fri,\x2023\x20Dec\x202022\x2018:46:57\x20GMT;\x20HttpOnly\r\n
SF:X-Content-Type-Options:\x20nosniff\r\nX-Frame-Options:\x20DENY\r\nDate:
SF:\x20Thu,\x2022\x20Dec\x202022\x2018:46:57\x20GMT\r\n\r\n<!DOCTYPE\x20ht
SF:ml>\n<html>\n<head\x20data-suburl=\"\">\n\t<meta\x20http-equiv=\"Conten
SF:t-Type\"\x20content=\"text/html;\x20charset=UTF-8\"\x20/>\n\t<meta\x20h
SF:ttp-equiv=\"X-UA-Compatible\"\x20content=\"IE=edge\"/>\n\t\n\t\t<meta\x
SF:20name=\"author\"\x20content=\"Gogs\"\x20/>\n\t\t<meta\x20name=\"descri
SF:ption\"\x20content=\"Gogs\x20is\x20a\x20painless\x20self-hosted\x20Git\
SF:x20service\"\x20/>\n\t\t<meta\x20name=\"keywords\"\x20content=\"go,\x20
SF:git,\x20self-hosted,\x20gogs\">\n\t\n\t<meta\x20name=\"referrer\"\x20co
SF:ntent=\"no-referrer\"\x20/>\n\t<meta\x20name=\"_csrf\"\x20content=\"IiA
SF:HGWuYSbIdjDQGwaA5sRUJ-gk6MTY3MTczNDgxNzU4M")%r(FourOhFourRequest,1C3E,"
SF:HTTP/1\.0\x20404\x20Not\x20Found\r\nContent-Type:\x20text/html;\x20char
SF:set=UTF-8\r\nSet-Cookie:\x20lang=en-US;\x20Path=/;\x20Max-Age=214748364
SF:7\r\nSet-Cookie:\x20i_like_gogs=6f281cd94540c4c7;\x20Path=/;\x20HttpOnl
SF:y\r\nSet-Cookie:\x20_csrf=OKv2gZc31tdlL7qsvpPgcFJ8TSs6MTY3MTczNDgyMzE5O
SF:Dg4NDMxNA;\x20Path=/;\x20Domain=assignment\.pg;\x20Expires=Fri,\x2023\x
SF:20Dec\x202022\x2018:47:03\x20GMT;\x20HttpOnly\r\nX-Content-Type-Options
SF::\x20nosniff\r\nX-Frame-Options:\x20DENY\r\nDate:\x20Thu,\x2022\x20Dec\
SF:x202022\x2018:47:03\x20GMT\r\n\r\n<!DOCTYPE\x20html>\n<html>\n<head\x20
SF:data-suburl=\"\">\n\t<meta\x20http-equiv=\"Content-Type\"\x20content=\"
SF:text/html;\x20charset=UTF-8\"\x20/>\n\t<meta\x20http-equiv=\"X-UA-Compa
SF:tible\"\x20content=\"IE=edge\"/>\n\t\n\t\t<meta\x20name=\"author\"\x20c
SF:ontent=\"Gogs\"\x20/>\n\t\t<meta\x20name=\"description\"\x20content=\"G
SF:ogs\x20is\x20a\x20painless\x20self-hosted\x20Git\x20service\"\x20/>\n\t
SF:\t<meta\x20name=\"keywords\"\x20content=\"go,\x20git,\x20self-hosted,\x
SF:20gogs\">\n\t\n\t<meta\x20name=\"referrer\"\x20content=\"no-referrer\"\
SF:x20/>\n\t<meta\x20name=\"_csrf\"\x20content=\"OKv2gZc31tdlL7qsvpPgcFJ8T
SF:Ss6MTY3MTczND");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Dec 22 19:48:48 2022 -- 1 IP address (1 host up) scanned in 160.47 seconds
```

