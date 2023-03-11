First start enumeration with nmap 

```
# Nmap 7.93 scan initiated Sat Mar 11 02:58:09 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80,3306,33060 -Pn 192.168.0.102
Nmap scan report for 192.168.0.102                                                 
Host is up (0.00047s latency).                                                     
                                                                                   
PORT      STATE SERVICE VERSION                                                    
22/tcp    open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)     
| ssh-hostkey:                                                                     
|   3072 34d64117226139afdda754ff2d3d98a7 (RSA)                           
|   256 eba62467cad00e9e84febeb5c2788446 (ECDSA)                          
|_  256 9730a6c7f4eebd5078938cdc7dc24b0a (ED25519)                        
80/tcp    open  http    Apache httpd 2.4.54 ((Debian))                    
|_http-title: PainVM                                                               
|_http-server-header: Apache/2.4.54 (Debian)                              
3306/tcp  open  mysql   MySQL (unauthorized)                              
33060/tcp open  mysqlx?                                                            
| fingerprint-strings:                                                             
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"                                                             
|     HY000                                                                        
|   LDAPBindReq:                                                                   
|     *Parse error unserializing protobuf message"                        
|     HY000                                                                        
|   oracle-tns:                                                                    
|     Invalid message-frame."                                                      
|_    HY000                                                                        
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-ser
vice :                                                                             
SF-Port33060-TCP:V=7.93%I=7%D=3/11%Time=640BE039%P=x86_64-pc-linux-gnu%r(N
SF:ULL,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(GenericLines,9,"\x05\0\0\0\x0b\
SF:x08\x05\x1a\0")%r(GetRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(HTTPOp
SF:tions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(RTSPRequest,9,"\x05\0\0\0\x0b
SF:\x08\x05\x1a\0")%r(RPCCheck,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSVers
SF:ionBindReqTCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSStatusRequestTCP,2                                                                                            
SF:B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fI                                                                                            
SF:nvalid\x20message\"\x05HY000")%r(Help,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")
SF:%r(SSLSessionReq,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01
SF:\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(TerminalServerCookie
SF:8\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\
SF:x05HY000")%r(LDAPBindReq,46,"\x05\0\0\0\x0b\x08\x05\x1a\x009\0\0\0\x01\
SF:x08\x01\x10\x88'\x1a\*Parse\x20error\x20unserializing\x20protobuf\x20me
SF:ssage\"\x05HY000")%r(SIPOptions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LAN
SF:Desk-RC,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TerminalServer,9,"\x05\0\0\
SF:0\x0b\x08\x05\x1a\0")%r(NCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(NotesRP
SF:C,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x
SF:0fInvalid\x20message\"\x05HY000")%r(JavaRMI,9,"\x05\0\0\0\x0b\x08\x05\x
SF:1a\0")%r(WMSRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(oracle-tns,32,"
SF:\x05\0\0\0\x0b\x08\x05\x1a\0%\0\0\0\x01\x08\x01\x10\x88'\x1a\x16Invalid
SF:\x20message-frame\.\"\x05HY000")%r(ms-sql-s,9,"\x05\0\0\0\x0b\x08\x05\x
SF:1a\0")%r(afp,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10
SF:\x88'\x1a\x0fInvalid\x20message\"\x05HY000");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Mar 11 02:58:24 2023 -- 1 IP address (1 host up) scanned in 14.97 seconds
```
cool we know what ports are open. 

![image](https://user-images.githubusercontent.com/87468669/224459020-ea97bd89-4c8e-4ae3-9537-623551730ccc.png)

noticed a webserver running on port 80, opening it on a browser w go directed to an homepage with pain quotes... 😂 we about to face pain here. nothing to fear we've got sharingan. lt's burst hidden directories.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/Pain]                                                                                                              
└─$ gobuster dir -u "http://192.168.0.102/" -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt -x txt,php,bak,zip,tar,sql 2>/dev/null            
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.0.102/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5 
[+] Extensions:              zip,tar,sql,txt,php,bak
[+] Timeout:                 10s
===============================================================
2023/03/11 03:22:17 Starting gobuster in directory enumeration mode
===============================================================
/js                   (Status: 301) [Size: 311] [--> http://192.168.0.102/js/]
/img                  (Status: 301) [Size: 312] [--> http://192.168.0.102/img/]
/index.php            (Status: 200) [Size: 3220]
/fonts                (Status: 301) [Size: 314] [--> http://192.168.0.102/fonts/]
/server-status        (Status: 403) [Size: 278]
/index.php            (Status: 200) [Size: 3220]
/pain_management.php  (Status: 200) [Size: 1903]
/.php                 (Status: 403) [Size: 278]

===============================================================
2023/03/11 03:24:28 Finished
===============================================================
```
cool we got a `/pain_management.php` let's check it out. 

![image](https://user-images.githubusercontent.com/87468669/224460581-e7e63462-246e-4d5f-9820-cb2bb21173f9.png)

navigating to the dir, we got directed to a login page. let's play around. 

![image](https://user-images.githubusercontent.com/87468669/224461729-9168c77c-7609-4347-a772-67d540140019.png)

remember we have mangekyou sharingan ❟❛❟ was able to guess the cred. trying creds `admin:pain` we got in. now let's play around more

![image](https://user-images.githubusercontent.com/87468669/224462405-86169775-d067-461b-9897-08621becf571.png)

navigated to `New_mission.php`, viewing the page source we got to see some comments.

```
<!-- Todo: 

- Gedo_Statue.php 
- Add a progress bar to show the upload progress
- Implement client-side validation to check file type and size before uploading
- Add a preview of the selected image before uploading
- Include a confirmation message after successful upload
- Add a button to clear the form after submission
- Implement server-side validation to prevent malicious uploads
- Automatically resize the uploaded image to a maximum size
- Add a report back function to notify Pain the Akatsuki leader of new uploads -->
```
🤔hmmmmm... let's check out the `Gedo_Statue.php` dir first. 

![image](https://user-images.githubusercontent.com/87468669/224462633-fd182772-5aed-4b74-accd-c0bb4d1e6323.png)

cool we have an upload form page. let's try upload a php web shell.

