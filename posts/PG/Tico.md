First start with nmap enumeration

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Sat Jan  7 11:07:30 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.202.143
Nmap scan report for 192.168.202.143
Host is up (0.15s latency).
Not shown: 65432 filtered tcp ports (no-response), 97 closed tcp ports (conn-refused)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
80/tcp    open  http
8080/tcp  open  http-proxy
11211/tcp open  memcache
27017/tcp open  mongod

Read data files from: /usr/bin/../share/nmap
# Nmap done at Sat Jan  7 11:07:54 2023 -- 1 IP address (1 host up) scanned in 24.37 seconds
```

cools we know what ports are open, let's run default nmap scripts to know what services are running on those ports.

```
# Nmap 7.93 scan initiated Sat Jan  7 10:11:07 2023 as: nmap -sC -sV -oN normal.tcp -p 21,22,80,8080,11211,27017 -Pn 192.168.202.143
Nmap scan report for 192.168.202.143                                               
Host is up (0.15s latency).                                                        
                                                                                   
PORT      STATE SERVICE    VERSION                                                 
21/tcp    open  ftp        vsftpd 3.0.3                                            
| ftp-syst:                                                                        
|   STAT:                                                                          
| FTP server status:                                                               
|      Connected to 192.168.49.202                                                 
|      Logged in as ftp                                                            
|      TYPE: ASCII                                                                                                                                                    
|      No session bandwidth limit                                                                                                                                     
|      Session timeout in seconds is 300                                           
|      Control connection is plain text                                            
|      Data connections will be plain text                                         
|      At session startup, client count was 2                                      
|      vsFTPd 3.0.3 - secure, fast, stable                                         
|_End of status                                                                    
| ftp-anon: Anonymous FTP login allowed (FTP code 230)                             
|_drwxr-xr-x    2 ftp      ftp          4096 Feb 01  2021 pub                      
22/tcp    open  ssh        OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)     
| ssh-hostkey:                                                                     
|   2048 8535fbcab34b30d8e58eb325586c6e70 (RSA)                                                                                                                       
|   256 de67a232d5ff566e825b6a177de244ac (ECDSA)                                                                                                                      
|_  256 3aa3203b32cd836fdc23a266f90fc6d3 (ED25519)                                 
80/tcp    open  http       nginx 1.14.0 (Ubuntu)                                   
|_http-server-header: nginx/1.14.0 (Ubuntu)                                                                                                                           
|_http-title: Markdown Editor                                                                                                                                         
8080/tcp  open  http-proxy                                                         
| http-robots.txt: 3 disallowed entries                                            
|_/admin/ /reset/ /compose                                                                                                                                            
| fingerprint-strings:                                                                                                                                                
|   FourOhFourRequest:                                                                                                                                                
|     HTTP/1.1 404 Not Found                                                       
|     X-DNS-Prefetch-Control: off                                                  
|     X-Frame-Options: SAMEORIGIN 
|     X-Download-Options: noopen                                                                                                                                      
|     X-Content-Type-Options: nosniff                                                                                                                                 
|     X-XSS-Protection: 1; mode=block
|     Referrer-Policy: strict-origin-when-cross-origin
|     X-Powered-By: NodeBB
|     set-cookie: _csrf=d45fYv6qykgQfOh_F298tWSq; Path=/
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 15431
|     ETag: W/"3c47-dOOAemd39DFLPRtKZhkrDysDlO0"
|     Vary: Accept-Encoding
|     Date: Sat, 07 Jan 2023 09:11:16 GMT 
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en-GB" data-dir="ltr" style="direction: ltr;" >
|     <head>
|     <title>Not Found | NodeBB</title>
|     <meta name="viewport" content="width&#x3D;device-width, initial-scale&#x3D;1.0" />
|     <meta name="content-type" content="text/html; charset=UTF-8" />
|     <meta name="apple-mobile-web-app-capable" content="yes" />
|     <meta name="mobile-web-app-capable" content="yes" />
|     <meta property="og:site_name"
|   GetRequest: 
|     HTTP/1.1 200 OK
|     X-DNS-Prefetch-Control: off
|     X-Frame-Options: SAMEORIGIN
|     X-Download-Options: noopen
|     X-Content-Type-Options: nosniff
|     X-XSS-Protection: 1; mode=block
|     Referrer-Policy: strict-origin-when-cross-origin
|     X-Powered-By: NodeBB
|     set-cookie: _csrf=QqXoNv1rGWr55IpJmjfZviC-; Path=/
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 24233
|     ETag: W/"5ea9-vF3QEjNSz8roc5HAM/GGDnJcZDM"
|     Vary: Accept-Encoding
|     Date: Sat, 07 Jan 2023 09:11:15 GMT 
|     Connection: close
|     <!DOCTYPE html>
|     <head>                                                                                                                                                 [102/181]
|     <title>Home | NodeBB</title>
|     <meta name="viewport" content="width&#x3D;device-width, initial-scale&#x3D;1.0" />
|     <meta name="content-type" content="text/html; charset=UTF-8" />
|     <meta name="apple-mobile-web-app-capable" content="yes" />
|     <meta name="mobile-web-app-capable" content="yes" />
|     <meta property="og:site_name" content="No
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     X-DNS-Prefetch-Control: off
|     X-Frame-Options: SAMEORIGIN
|     X-Download-Options: noopen
|     X-Content-Type-Options: nosniff
|     X-XSS-Protection: 1; mode=block
|     Referrer-Policy: strict-origin-when-cross-origin
|     X-Powered-By: NodeBB
|     Allow: GET,HEAD
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 8
|     ETag: W/"8-ZRAf8oNBS3Bjb/SU2GYZCmbtmXg"
|     Vary: Accept-Encoding
|     Date: Sat, 07 Jan 2023 09:11:16 GMT 
|     Connection: close
|     GET,HEAD
|   RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|_    Connection: close
|_http-title: Home | NodeBB
11211/tcp open  memcached  Memcached 1.5.6 (uptime 599 seconds; Ubuntu)
27017/tcp open  mongodb    MongoDB
| mongodb-databases: 
|   codeName = Unauthorized
|   errmsg = command listDatabases requires authentication
|   ok = 0.0
|_  code = 13
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest: 
|     HTTP/1.0 200 OK
|     Connection: close
|     Content-Type: text/plain
|     Content-Length: 85                                                                                                                                      [62/181]
|     looks like you are trying to access MongoDB over HTTP on the native driver port.
|   mongodb: 
|     errmsg
|     command serverStatus requires authentication
|     code
|     codeName
|_    Unauthorized
| mongodb-info: 
|   MongoDB Build info
|     modules
|     gitVersion = 1741806fb46c161a1d42870f6e98f5100d196315
|     javascriptEngine = mozjs
|     sysInfo = deprecated
|     debug = false
|     buildEnvironment
|       ccflags = -fno-omit-frame-pointer -fno-strict-aliasing -ggdb -pthread -Wall -Wsign-compare -Wno-unknown-pragmas -Winvalid-pch -Werror -O2 -Wno-unused-local-ty
pedefs -Wno-unused-function -Wno-deprecated-declarations -Wno-unused-but-set-variable -Wno-missing-braces -fstack-protector-strong -fno-builtin-memcmp
|       cc = /opt/mongodbtoolchain/v2/bin/gcc: gcc (GCC) 5.4.0
|       distmod = ubuntu1804
|       linkflags = -pthread -Wl,-z,now -rdynamic -Wl,--fatal-warnings -fstack-protector-strong -fuse-ld=gold -Wl,--build-id -Wl,--hash-style=gnu -Wl,-z,noexecstack -
Wl,--warn-execstack -Wl,-z,relro
|       target_arch = x86_64
|       target_os = linux
|       cxx = /opt/mongodbtoolchain/v2/bin/g++: g++ (GCC) 5.4.0
|       cxxflags = -Woverloaded-virtual -Wno-maybe-uninitialized -std=c++14
|       distarch = x86_64
|     openssl
|       compiled = OpenSSL 1.1.1  11 Sep 2018
|       running = OpenSSL 1.1.1  11 Sep 2018
|     maxBsonObjectSize = 16777216
|     bits = 64
|     storageEngines
|       3 = wiredTiger
|       2 = mmapv1
|       1 = ephemeralForTest
|       0 = devnull
|     version = 4.0.22
|     versionArray
|       3 = 0
|       2 = 22                                                                                                                                                [22/181]
|       1 = 0
|       0 = 4
|     ok = 1.0
|     allocator = tcmalloc
|   Server status
|     codeName = Unauthorized
|     errmsg = command serverStatus requires authentication
|     ok = 0.0
|_    code = 13
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-ser
vice :
SF-Port8080-TCP:V=7.93%I=7%D=1/7%Time=63B93733%P=x86_64-pc-linux-gnu%r(Get
SF:Request,24CE,"HTTP/1\.1\x20200\x20OK\r\nX-DNS-Prefetch-Control:\x20off\
SF:r\nX-Frame-Options:\x20SAMEORIGIN\r\nX-Download-Options:\x20noopen\r\nX
SF:-Content-Type-Options:\x20nosniff\r\nX-XSS-Protection:\x201;\x20mode=bl
SF:ock\r\nReferrer-Policy:\x20strict-origin-when-cross-origin\r\nX-Powered
SF:-By:\x20NodeBB\r\nset-cookie:\x20_csrf=QqXoNv1rGWr55IpJmjfZviC-;\x20Pat
SF:h=/\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\
SF:x2024233\r\nETag:\x20W/\"5ea9-vF3QEjNSz8roc5HAM/GGDnJcZDM\"\r\nVary:\x2
SF:0Accept-Encoding\r\nDate:\x20Sat,\x2007\x20Jan\x202023\x2009:11:15\x20G
SF:MT\r\nConnection:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"e
SF:n-GB\"\x20data-dir=\"ltr\"\x20style=\"direction:\x20ltr;\"\x20\x20>\n<h
SF:ead>\n\t<title>Home\x20\|\x20NodeBB</title>\n\t<meta\x20name=\"viewport
SF:\"\x20content=\"width&#x3D;device-width,\x20initial-scale&#x3D;1\.0\"\x
SF:20/>\n\t<meta\x20name=\"content-type\"\x20content=\"text/html;\x20chars
SF:et=UTF-8\"\x20/>\n\t<meta\x20name=\"apple-mobile-web-app-capable\"\x20c
SF:ontent=\"yes\"\x20/>\n\t<meta\x20name=\"mobile-web-app-capable\"\x20con
SF:tent=\"yes\"\x20/>\n\t<meta\x20property=\"og:site_name\"\x20content=\"N
SF:o")%r(HTTPOptions,1BF,"HTTP/1\.1\x20200\x20OK\r\nX-DNS-Prefetch-Control
SF::\x20off\r\nX-Frame-Options:\x20SAMEORIGIN\r\nX-Download-Options:\x20no
SF:open\r\nX-Content-Type-Options:\x20nosniff\r\nX-XSS-Protection:\x201;\x
SF:20mode=block\r\nReferrer-Policy:\x20strict-origin-when-cross-origin\r\n
SF:X-Powered-By:\x20NodeBB\r\nAllow:\x20GET,HEAD\r\nContent-Type:\x20text/
SF:html;\x20charset=utf-8\r\nContent-Length:\x208\r\nETag:\x20W/\"8-ZRAf8o
SF:NBS3Bjb/SU2GYZCmbtmXg\"\r\nVary:\x20Accept-Encoding\r\nDate:\x20Sat,\x2
SF:007\x20Jan\x202023\x2009:11:16\x20GMT\r\nConnection:\x20close\r\n\r\nGE
SF:T,HEAD")%r(RTSPRequest,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnec
SF:tion:\x20close\r\n\r\n")%r(FourOhFourRequest,3494,"HTTP/1\.1\x20404\x20
SF:Not\x20Found\r\nX-DNS-Prefetch-Control:\x20off\r\nX-Frame-Options:\x20S
SF:AMEORIGIN\r\nX-Download-Options:\x20noopen\r\nX-Content-Type-Options:\x
SF:20nosniff\r\nX-XSS-Protection:\x201;\x20mode=block\r\nReferrer-Policy:\
SF:x20strict-origin-when-cross-origin\r\nX-Powered-By:\x20NodeBB\r\nset-co
SF:okie:\x20_csrf=d45fYv6qykgQfOh_F298tWSq;\x20Path=/\r\nContent-Type:\x20
SF:text/html;\x20charset=utf-8\r\nContent-Length:\x2015431\r\nETag:\x20W/\
SF:"3c47-dOOAemd39DFLPRtKZhkrDysDlO0\"\r\nVary:\x20Accept-Encoding\r\nDate
SF::\x20Sat,\x2007\x20Jan\x202023\x2009:11:16\x20GMT\r\nConnection:\x20clo
SF:se\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en-GB\"\x20data-dir=\"ltr
SF:\"\x20style=\"direction:\x20ltr;\"\x20\x20>\n<head>\n\t<title>Not\x20Fo
SF:und\x20\|\x20NodeBB</title>\n\t<meta\x20name=\"viewport\"\x20content=\"
SF:width&#x3D;device-width,\x20initial-scale&#x3D;1\.0\"\x20/>\n\t<meta\x2
SF:0name=\"content-type\"\x20content=\"text/html;\x20charset=UTF-8\"\x20/>
SF:\n\t<meta\x20name=\"apple-mobile-web-app-capable\"\x20content=\"yes\"\x
SF:20/>\n\t<meta\x20name=\"mobile-web-app-capable\"\x20content=\"yes\"\x20
SF:/>\n\t<meta\x20property=\"og:site_name\"");
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jan  7 10:12:17 2023 -- 1 IP address (1 host up) scanned in 70.81 seconds
```

Now w know what services are  running on those port, let start by enumerating port 21 (ftp).

![image](https://user-images.githubusercontent.com/87468669/211146595-f83c797b-5dc6-4df5-ae61-03b6e844c3d3.png)

Anonymous login was allowed, and i found a pcap file in the /pub dir. let's use wireshark to analyse the pcap file.

![image](https://user-images.githubusercontent.com/87468669/211146721-b91628ed-2f26-4c98-85a7-61eb29671eb0.png)

Downloaded and analyzed the pcap file, i got nothing usefull there... maybe it's a rabbit hole🐰. Let's move to the next port: 8080 (http)

![image](https://user-images.githubusercontent.com/87468669/211146767-88589404-e64e-4fd6-a3e0-75a951a6ea6b.png)

Hmmmm🤔 the web service is running NodeBB. i've exploited one of this vuln before, which is accout takeover, i.e priv escalation from a normal user to admin. First created an acctount 

![image](https://user-images.githubusercontent.com/87468669/211147166-9d3a9a0b-a522-4cf4-8260-9d1bbe2d30aa.png)

Register an acct complete the registration process.

![image](https://user-images.githubusercontent.com/87468669/211147430-1bbc0278-5f6b-4ae3-b6ee-3300bfcb7ba1.png)

navvigate to the change password path.

![image](https://user-images.githubusercontent.com/87468669/211147832-c6dc560e-745f-4651-9768-954ffbf2af75.png)

Change the password and intercept with burp.

![image](https://user-images.githubusercontent.com/87468669/211147851-eb312130-8523-47f7-87f1-b43c58eac343.png)

Changed the uid from 2 to 1, and forward the request. logot and login as admin with the changed password.

![image](https://user-images.githubusercontent.com/87468669/211148476-86715fb1-cdf8-4f36-a4fc-5f814496bde2.png)

And Bull's eye we logged in as admin, let's get shell.

![image](https://user-images.githubusercontent.com/87468669/211148661-88383784-bfda-40dc-9ea9-e82b077ee201.png)

checking search sploit for public exploit, i found one that does arbitary file write.

![image](https://user-images.githubusercontent.com/87468669/211148917-1593aa96-cca5-4800-b99c-93edd2e6f415.png)

So what this exploit does is that it replace the target root authorized key which will give use access to ssh as root using our local id_rsa key into the target. Modified the required places.

![image](https://user-images.githubusercontent.com/87468669/211149237-ed5d4cfc-3fc0-4019-9e17-522f2556a528.png)

Run the exploit and ssh using our id_rsa key as user root and we got root🤓🤓

