first enumeration with nmap

```
# Nmap 7.93 scan initiated Fri Mar 10 22:52:02 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80,5000,31337 -Pn 192.168.63.102                                             
Nmap scan report for 192.168.63.102                                                                                                                                    
Host is up (0.14s latency).                                                        
                                                                                   
PORT      STATE SERVICE VERSION                                                    
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:                                                                                                                                                         
|   2048 e64423acb2d982e79058155e4023ed65 (RSA)                                    
|   256 ae04856ecb104f554aad969ef2ce184f (ECDSA)                          
|_  256 f708561997b5031018667e7d2e0a4742 (ED25519)                        
80/tcp    open  http    lighttpd 1.4.45                                            
|_http-title: Custom-ers                                                           
|_http-server-header: lighttpd/1.4.45                                              
5000/tcp  open  http    Werkzeug httpd 1.0.1 (Python 3.6.9)               
|_http-server-header: Werkzeug/1.0.1 Python/3.6.9                         
|_http-title: Site doesn't have a title (text/html; charset=utf-8).                                                                                                    
31337/tcp open  Elite?                                                             
| fingerprint-strings:                                                             
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, NULL:                      
|     username>                                                                    
|   GenericLines, GetRequest, HTTPOptions, RTSPRequest, SIPOptions:       
|     username> password> authentication failed                                    
|   Help:                                                                          
|     username> password>                                                          
|   RPCCheck:                                                                                                                                                          
|     username> Traceback (most recent call last):                                                                                                                     
|     File "/opt/.tick-serv/tickets.py", line 105, in <module>                     
|     main()                                                                       
|     File "/opt/.tick-serv/tickets.py", line 93, in main                 
|     username = input("username> ")                                               
|     File "/usr/lib/python3.6/codecs.py", line 321, in decode                                                                                                         
|     (result, consumed) = self._buffer_decode(data, self.errors, final)                                                                                               
|     UnicodeDecodeError: 'utf-8' codec can't decode byte 0x80 in position 0: invalid start byte
|   SSLSessionReq:                                                                 
|     username> Traceback (most recent call last):                                                                                                              [25/62]
|     File "/opt/.tick-serv/tickets.py", line 105, in <module>                                                                                                         
|     main()                                                                                                                                                           
|     File "/opt/.tick-serv/tickets.py", line 93, in main                                                                                                              
|     username = input("username> ")                                               
|     File "/usr/lib/python3.6/codecs.py", line 321, in decode                     
|     (result, consumed) = self._buffer_decode(data, self.errors, final)           
|     UnicodeDecodeError: 'utf-8' codec can't decode byte 0xd7 in position 13: invalid continuation byte
|   TerminalServerCookie:                                                                                                                                              
|     username> Traceback (most recent call last):                                 
|     File "/opt/.tick-serv/tickets.py", line 105, in <module>            
|     main()                                                                       
|     File "/opt/.tick-serv/tickets.py", line 93, in main                          
|     username = input("username> ")                                               
|     File "/usr/lib/python3.6/codecs.py", line 321, in decode                     
|     (result, consumed) = self._buffer_decode(data, self.errors, final)  
|_    UnicodeDecodeError: 'utf-8' codec can't decode byte 0xe0 in position 5: invalid continuation byte
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-serv
ice :                                                                              
SF-Port31337-TCP:V=7.93%I=7%D=3/10%Time=640BA68B%P=x86_64-pc-linux-gnu%r(N         
SF:ULL,A,"username>\x20")%r(GetRequest,2A,"username>\x20password>\x20authe
SF:ntication\x20failed\n")%r(SIPOptions,2A,"username>\x20password>\x20auth         
SF:entication\x20failed\n")%r(GenericLines,2A,"username>\x20password>\x20a
SF:uthentication\x20failed\n")%r(HTTPOptions,2A,"username>\x20password>\x2         
SF:0authentication\x20failed\n")%r(RTSPRequest,2A,"username>\x20password>\         
SF:x20authentication\x20failed\n")%r(RPCCheck,1A9,"username>\x20Traceback\         
SF:x20\(most\x20recent\x20call\x20last\):\n\x20\x20File\x20\"/opt/\.tick-s                                                                                             
SF:erv/tickets\.py\",\x20line\x20105,\x20in\x20<module>\n\x20\x20\x20\x20m 
```
