First start with an nmap scan 

`nmap -sC -sV -T4 -oN nmap.txt <IP> -p-`

In this box, i'm ran full port scan

```
# Nmap 7.93 scan initiated Tue Dec 27 20:43:17 2022 as: nmap -sC -sV -T4 -oN fullmap.txt -Pn -p- 192.168.218.69
Nmap scan report for 192.168.218.69                                                
Host is up (0.20s latency).                                                        
Not shown: 65529 filtered tcp ports (no-response)                                  
PORT      STATE  SERVICE    VERSION                                                
22/tcp    open   ssh        OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)          
| ssh-hostkey:                                                                     
|   2048 098039ef3f61a8d9e6fb049423c9efa8 (RSA)                                                                                                                       
|   256 83f86f507a6205aa154410f54ac2f5a6 (ECDSA)                                   
|_  256 1e2b13305cf13115b4e8f3d2c4e805b5 (ED25519)                                 
53/tcp    closed domain                                                            
80/tcp    open   http       nginx 1.10.3                                           
|_http-title: Welcome to nginx!                                                    
|_http-server-header: nginx/1.10.3                                                                                                                                    
6379/tcp  open   redis      Redis key-value store 5.0.9                            
8080/tcp  open   http-proxy                                                        
|_http-title: Home | NodeBB                                                        
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
|     set-cookie: _csrf=d5uzX_2hYSP31L7lUzbQBBmO; Path=/                           
|     Content-Type: text/html; charset=utf-8                                       
|     Content-Length: 11098                                                        
|     ETag: W/"2b5a-p0+LsDcr7QI4Uy1JUbCon0pbe1A"                                                                                                                      
|     Vary: Accept-Encoding                                                                                                                                           
|     Date: Tue, 27 Dec 2022 19:50:50 GMT                                                                                                                             
|     Connection: close                                                            
|     <!DOCTYPE html> 
|     <html lang="en-GB" data-dir="ltr" style="direction: ltr;" >                  
|     <head>                                                                                                                                                          
|     <title>Not Found | NodeBB</title>                                            
|     <meta name="viewport" content="width&#x3D;device-width, initial-scale&#x3D;1.0" />
|     <meta name="content-type" content="text/html; charset=UTF-8" />              
|     <meta name="apple-mobile-web-app-capable" content="yes" />                   
|     <meta name="mobile-web-app-capable" content="yes" />                         
|     <meta property="og:site_n                                                    
|   GetRequest:                                                                                                                                                       
|     HTTP/1.1 200 OK                                                              
|     X-DNS-Prefetch-Control: off                                                  
|     X-Frame-Options: SAMEORIGIN                                                  
|     X-Download-Options: noopen                                                   
|     X-Content-Type-Options: nosniff                                              
|     X-XSS-Protection: 1; mode=block                                                                                                                                 
|     Referrer-Policy: strict-origin-when-cross-origin                             
|     X-Powered-By: NodeBB                                                         
|     set-cookie: _csrf=ONWeS158w5oe-yCqMrGw94SJ; Path=/                           
|     Content-Type: text/html; charset=utf-8                                       
|     Content-Length: 18181                                                        
|     ETag: W/"4705-UKx1fv0k9B8XgnkeHgbpNP8ZMe4"                                   
|     Vary: Accept-Encoding                                                        
|     Date: Tue, 27 Dec 2022 19:50:48 GMT                                          
|     Connection: close                                                            
|     <!DOCTYPE html>                                                                                                                                                 
|     <html lang="en-GB" data-dir="ltr" style="direction: ltr;" >                  
|     <head>                                                                       
|     <title>Home | NodeBB</title>                                                                                                                                    
|     <meta name="viewport" content="width&#x3D;device-width, initial-scale&#x3D;1.0" />
|     <meta name="content-type" content="text/html; charset=UTF-8" />              
|     <meta name="apple-mobile-web-app-capable" content="yes" />                   
|     <meta name="mobile-web-app-capable" content="yes" />                         
|     <meta property="og:site_name" content                                        
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
|     Date: Tue, 27 Dec 2022 19:50:49 GMT                                          
|     Connection: close                                                            
|     GET,HEAD                                                                     
|   RTSPRequest:                                                                   
|     HTTP/1.1 400 Bad Request                                                     
|_    Connection: close                                                                                                                                               
27017/tcp open   mongodb    MongoDB 4.0.18                                         
| fingerprint-strings:                                                             
|   GetRequest:                                                                    
|     HTTP/1.0 200 OK                                                              
|     Connection: close                                                            
|     Content-Type: text/plain                                                     
|     Content-Length: 85                                                           
|     looks like you are trying to access MongoDB over HTTP on the native driver port.
|   mongodb:                                                                       
|     errmsg                                                                                                                                                          
|     command serverStatus requires authentication                                 
|     code                                                                         
|     codeName                                                                                                                                                        
|_    Unauthorized                                                                                                                                                    
| mongodb-databases:                                                               
|   codeName = Unauthorized                                                        
|   code = 13                                                                      
|   errmsg = command listDatabases requires authentication                         
|_  ok = 0.0                                                                                                                                                          
| mongodb-info:                                                                                                                                                       
|   MongoDB Build info                                                                                                                                                
|     sysInfo = deprecated                                                         
|     gitVersion = 6883bdfb8b8cff32176b1fd176df04da9165fd67
|     javascriptEngine = mozjs                                                     
|     openssl                                                                                                                                                         
|       compiled = OpenSSL 1.1.0l  10 Sep 2019                                     
|       running = OpenSSL 1.1.0l  10 Sep 2019                                                                                                                         
|     bits = 64                                                                    
|     buildEnvironment                                                             
|       target_arch = x86_64                                                       
|       distarch = x86_64                                                          
|       cxxflags = -Woverloaded-virtual -Wno-maybe-uninitialized -std=c++14                                                                                           
|       ccflags = -fno-omit-frame-pointer -fno-strict-aliasing -ggdb -pthread -Wall -Wsign-compare -Wno-unknown-pragmas -Winvalid-pch -Werror -O2 -Wno-unused-local-ty
pedefs -Wno-unused-function -Wno-deprecated-declarations -Wno-unused-but-set-variable -Wno-missing-braces -fstack-protector-strong -fno-builtin-memcmp
|       linkflags = -pthread -Wl,-z,now -rdynamic -Wl,--fatal-warnings -fstack-protector-strong -fuse-ld=gold -Wl,--build-id -Wl,--hash-style=gnu -Wl,-z,noexecstack -
Wl,--warn-execstack -Wl,-z,relro                                                   
|       distmod = debian92                                                         
|       cxx = /opt/mongodbtoolchain/v2/bin/g++: g++ (GCC) 5.4.0                                                                                                       
|       target_os = linux                                                          
|       cc = /opt/mongodbtoolchain/v2/bin/gcc: gcc (GCC) 5.4.0                     
|     maxBsonObjectSize = 16777216                                                 
|     storageEngines                                                               
|       0 = devnull                                                                
|       1 = ephemeralForTest                                                       
|       2 = mmapv1                                                                 
|       3 = wiredTiger                                                                                                                                                
|     modules                                                                      
|     ok = 1.0                                                                                                                                                        
|     debug = false                                                                
|     versionArray                                                                 
|       0 = 4                                                                                                                                                         
|       1 = 0                                                                                                                                                         
|       2 = 18                                                                     
|       3 = 0                                                                      
|     version = 4.0.18                                                             
|     allocator = tcmalloc                                                         
|   Server status                                                                                                                                                     
|     codeName = Unauthorized                                                                                                                                         
|     code = 13                                                                                                                                                       
|     errmsg = command serverStatus requires authentication                        
|_    ok = 0.0                                  
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-ser
vice :                                                                                                                                                                
SF-Port8080-TCP:V=7.93%I=7%D=12/27%Time=63AB4C98%P=x86_64-pc-linux-gnu%r(G         
SF:etRequest,2F52,"HTTP/1\.1\x20200\x20OK\r\nX-DNS-Prefetch-Control:\x20of                                                                                            
SF:f\r\nX-Frame-Options:\x20SAMEORIGIN\r\nX-Download-Options:\x20noopen\r\         
SF:nX-Content-Type-Options:\x20nosniff\r\nX-XSS-Protection:\x201;\x20mode=         
SF:block\r\nReferrer-Policy:\x20strict-origin-when-cross-origin\r\nX-Power         
SF:ed-By:\x20NodeBB\r\nset-cookie:\x20_csrf=ONWeS158w5oe-yCqMrGw94SJ;\x20P         
SF:ath=/\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length                                                                                            
SF::\x2018181\r\nETag:\x20W/\"4705-UKx1fv0k9B8XgnkeHgbpNP8ZMe4\"\r\nVary:\                                                                                            
SF:x20Accept-Encoding\r\nDate:\x20Tue,\x2027\x20Dec\x202022\x2019:50:48\x2                                                                                            
SF:0GMT\r\nConnection:\x20close\r\n\r\n<!DOCTYPE\x20html>\r\n<html\x20lang                                                                                            
SF:=\"en-GB\"\x20data-dir=\"ltr\"\x20style=\"direction:\x20ltr;\"\x20\x20>         
SF:\r\n<head>\r\n\t<title>Home\x20\|\x20NodeBB</title>\r\n\t<meta\x20name=         
SF:\"viewport\"\x20content=\"width&#x3D;device-width,\x20initial-scale&#x3                                                                                            
SF:D;1\.0\"\x20/>\n\t<meta\x20name=\"content-type\"\x20content=\"text/html         
SF:;\x20charset=UTF-8\"\x20/>\n\t<meta\x20name=\"apple-mobile-web-app-capa         
SF:ble\"\x20content=\"yes\"\x20/>\n\t<meta\x20name=\"mobile-web-app-capabl         
SF:e\"\x20content=\"yes\"\x20/>\n\t<meta\x20property=\"og:site_name\"\x20c         
SF:ontent")%r(HTTPOptions,1BF,"HTTP/1\.1\x20200\x20OK\r\nX-DNS-Prefetch-Co         
SF:ntrol:\x20off\r\nX-Frame-Options:\x20SAMEORIGIN\r\nX-Download-Options:\         
SF:x20noopen\r\nX-Content-Type-Options:\x20nosniff\r\nX-XSS-Protection:\x2         
SF:01;\x20mode=block\r\nReferrer-Policy:\x20strict-origin-when-cross-origi                                                                                            
SF:n\r\nX-Powered-By:\x20NodeBB\r\nAllow:\x20GET,HEAD\r\nContent-Type:\x20         
SF:text/html;\x20charset=utf-8\r\nContent-Length:\x208\r\nETag:\x20W/\"8-Z                                                                                            
SF:RAf8oNBS3Bjb/SU2GYZCmbtmXg\"\r\nVary:\x20Accept-Encoding\r\nDate:\x20Tu         
SF:e,\x2027\x20Dec\x202022\x2019:50:49\x20GMT\r\nConnection:\x20close\r\n\         
SF:r\nGET,HEAD")%r(RTSPRequest,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nC                                                                                            
SF:onnection:\x20close\r\n\r\n")%r(FourOhFourRequest,1A4A,"HTTP/1\.1\x2040                                                                                            
SF:4\x20Not\x20Found\r\nX-DNS-Prefetch-Control:\x20off\r\nX-Frame-Options:         
SF:\x20SAMEORIGIN\r\nX-Download-Options:\x20noopen\r\nX-Content-Type-Optio         
SF:ns:\x20nosniff\r\nX-XSS-Protection:\x201;\x20mode=block\r\nReferrer-Pol         
SF:icy:\x20strict-origin-when-cross-origin\r\nX-Powered-By:\x20NodeBB\r\ns         
SF:et-cookie:\x20_csrf=d5uzX_2hYSP31L7lUzbQBBmO;\x20Path=/\r\nContent-Type                                                                                            
SF::\x20text/html;\x20charset=utf-8\r\nContent-Length:\x2011098\r\nETag:\x                                                                                            
SF:20W/\"2b5a-p0\+LsDcr7QI4Uy1JUbCon0pbe1A\"\r\nVary:\x20Accept-Encoding\r                                                                                            
SF:\nDate:\x20Tue,\x2027\x20Dec\x202022\x2019:50:50\x20GMT\r\nConnection:\         
SF:x20close\r\n\r\n<!DOCTYPE\x20html>\r\n<html\x20lang=\"en-GB\"\x20data-d 
SF:ir=\"ltr\"\x20style=\"direction:\x20ltr;\"\x20\x20>\r\n<head>\r\n\t<tit                                                                                            
SF:le>Not\x20Found\x20\|\x20NodeBB</title>\r\n\t<meta\x20name=\"viewport\"         
SF:\x20content=\"width&#x3D;device-width,\x20initial-scale&#x3D;1\.0\"\x20         
SF:/>\n\t<meta\x20name=\"content-type\"\x20content=\"text/html;\x20charset                                                                                            
SF:=UTF-8\"\x20/>\n\t<meta\x20name=\"apple-mobile-web-app-capable\"\x20con                                                                                            
SF:tent=\"yes\"\x20/>\n\t<meta\x20name=\"mobile-web-app-capable\"\x20conte         
SF:nt=\"yes\"\x20/>\n\t<meta\x20property=\"og:site_n");                            
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel                            
                                                                                   
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                        
# Nmap done at Tue Dec 27 20:52:24 2022 -- 1 IP address (1 host up) scanned in 546.65 seconds
```

Got intresting ports open... i enumerated other ports, i got nothing so i went ahead to enumerate port 6379 which is a redis server.

![image](https://user-images.githubusercontent.com/87468669/209719951-1f964386-9926-410e-952b-9c52b7a45378.png)

Found an exploit for redis server on github `https://github.com/n0b0dyCN/redis-rogue-server.git` and setup ncat listener 

![image](https://user-images.githubusercontent.com/87468669/209720482-a5564260-4b5a-4952-9e7c-9c7f2d9d36c6.png)

check our listener, we got shell

![image](https://user-images.githubusercontent.com/87468669/209720613-b03b0dd1-c6e9-4603-a2f3-01e6c810d041.png)

Boom!!! got shell as user root.

![image](https://user-images.githubusercontent.com/87468669/209720880-3fcf9ab8-605c-49c3-b810-899e2c05c4c9.png)

Done😎😎
