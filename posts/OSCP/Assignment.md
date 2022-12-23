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

We have port 22 ssh, 80 and 8000 http. Let's enumerate port 80 first.

![image](https://user-images.githubusercontent.com/87468669/209358917-e11f4156-2e9e-4d8f-8eca-a5304f9779f0.png)

we have a note managing service running, let's register an account then login.

![image](https://user-images.githubusercontent.com/87468669/209359185-7e9eb50c-cfd2-495b-8088-73356fcafbae.png)

Boom we are in, let's create a new note... 

![image](https://user-images.githubusercontent.com/87468669/209359318-80d029b7-7f18-488a-9e91-7d58c5d3d4d4.png)

after that we can see the note created...

![image](https://user-images.githubusercontent.com/87468669/209359523-1cff2592-5066-4136-85de-1924c1d7ae26.png)

click on the members tab on the page 

![image](https://user-images.githubusercontent.com/87468669/209360491-44579a71-162b-4cbc-9bc5-f742da664750.png)

we get to see the available members with some creds... username forged_owner and password forged_owner

![image](https://user-images.githubusercontent.com/87468669/209360718-7ba60445-331f-4003-9916-007d15644e05.png)

Now let's try login with those creds 

![image](https://user-images.githubusercontent.com/87468669/209360863-f87ef02c-6ede-47e5-b2f9-6d2bbf9e7247.png)

we are in, what's next

![image](https://user-images.githubusercontent.com/87468669/209361180-42bf1301-a969-4d90-a90a-d14a10973eac.png)

Create a new note just like we did earlier...

![image](https://user-images.githubusercontent.com/87468669/209361347-5fc308b5-4dbe-4b82-8c1f-69ae707eb119.png)

Let's test the link paramter for idor by switing the number from 7 to 1.

![image](https://user-images.githubusercontent.com/87468669/209361476-61555807-948b-437c-9831-3915e01e31b9.png)

Great we got some creds which is for jane saying gogs... Let's enumerate port 8000

![image](https://user-images.githubusercontent.com/87468669/209361709-147a32dc-aac2-4386-aedd-6c932f69aede.png)

Let's login with the creds found earlier 

![image](https://user-images.githubusercontent.com/87468669/209361813-79bcce70-30f0-4f04-a74e-37f6d69f0a9c.png)

hit the login button 

![image](https://user-images.githubusercontent.com/87468669/209362053-e9407683-7840-47b0-b5d0-d99ff4c0c2bc.png)

Boom we are in, after playing around and got nothing hidden, i searched for ways in exploiting gogs and gt RCE.

![image](https://user-images.githubusercontent.com/87468669/209362861-1d4458a0-5b3f-46ae-87c9-a2730d80ee6f.png)

Almost every search brought msf exploit... man who does msf??? 🤔 I read the exploit and decided to do it manually.

![image](https://user-images.githubusercontent.com/87468669/209363288-a61801e7-96ad-4087-9eb2-e88d66bb4dca.png)

First create a new repo.

![image](https://user-images.githubusercontent.com/87468669/209363538-df9462e1-ed90-4181-aacd-c0a9c5a6921d.png)

After creating a repo, click on settings

![image](https://user-images.githubusercontent.com/87468669/209363724-a498bd38-602d-4bf5-9b04-9b8f169a4f69.png)

go to git hook, then click on the post receive edit button.

![image](https://user-images.githubusercontent.com/87468669/209363899-80c26f2c-bc39-4c1d-a1f7-e158b231d093.png)

put your reverse shell payload and update hook... after that go to your terminal 

![image](https://user-images.githubusercontent.com/87468669/209364440-c9b9532c-14b4-4572-a0e2-dc95d3098b0c.png)

start your ncat listener 

![image](https://user-images.githubusercontent.com/87468669/209364321-03573d6e-c56f-457c-935e-710e3a1794e9.png)

Next we git clone the repo

![image](https://user-images.githubusercontent.com/87468669/209364779-62214ae4-ef93-40d0-8fd3-9e06543ecc37.png)

![image](https://user-images.githubusercontent.com/87468669/209365129-427bc216-c7f9-4abd-8489-c823b58ac709.png)

```
touch README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin http://assignment.pg:8000/jane/Exploit.git
git push -u origin master
```
![image](https://user-images.githubusercontent.com/87468669/209365246-9150906d-15ba-4e6b-b1d4-7ea20933d3e5.png)

Boom we got a shell on ncat listener

![image](https://user-images.githubusercontent.com/87468669/209365462-65d82172-0759-4904-834b-0d9873185d6c.png)

Sabilize the shell

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
ctrl+z
export TERM=xterm256color
stty rows 40 cols 160
```

