First we start with nmap scan

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v 192.168.108.110`

```
# Nmap 7.93 scan initiated Thu Dec 29 10:18:40 2022 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v 192.168.108.110
Nmap scan report for 192.168.108.110
Host is up (0.20s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
80/tcp    open  http
3000/tcp  open  ppp
27017/tcp open  mongod

Read data files from: /usr/bin/../share/nmap
# Nmap done at Thu Dec 29 10:19:21 2022 -- 1 IP address (1 host up) scanned in 41.77 seconds
```

Got few ports open, let's run nmap script to know the services running on these ports.

```
# Nmap 7.93 scan initiated Thu Dec 29 10:23:33 2022 as: nmap -sC -sV -oN normal.tcp -p 21,22,80,3000,27017 -Pn 192.168.108.110                                        
Nmap scan report for 192.168.108.110                                               
Host is up (0.30s latency).                                                        
                                                                                   
PORT      STATE SERVICE VERSION                                                                                                                                       
21/tcp    open  ftp     vsftpd 3.0.3                                                                                                                                  
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
|      At session startup, client count was 2                                                                                                                         
|      vsFTPd 3.0.3 - secure, fast, stable                                                                                                                            
|_End of status                                                                    
| ftp-anon: Anonymous FTP login allowed (FTP code 230)                             
|_Can't get directory listing: TIMEOUT                                                                                                                                
22/tcp    open  ssh     OpenSSH 8.3 (protocol 2.0)                                 
| ssh-hostkey:                                                                     
|   3072 9d3feb1baa9c1eb1309b23534bcf5975 (RSA)                                    
|   256 cddc05e6e3bb1233f7097450128a8564 (ECDSA)                                   
|_  256 a0901f5078b39e412a7f5c6f4d0ea1fa (ED25519)                                 
80/tcp    open  http    Apache httpd 2.4.46 ((Fedora))                             
| http-robots.txt: 22 disallowed entries (15 shown)                                                                                                                   
| /core/ /profiles/ /README.txt /web.config /admin/                                
| /comment/reply/ /filter/tips /node/add/ /search/ /user/register/                                                                                                    
| /user/password/ /user/login/ /user/logout/ /index.php/admin/                     
|_/index.php/comment/reply/                                                        
|_http-server-header: Apache/2.4.46 (Fedora)                                                                                                                          
|_http-generator: Drupal 9 (https://www.drupal.org)                                                                                                                   
|_http-title: Home | Hacking Articles                                              
3000/tcp  open  http    Node.js (Express middleware)                                                                                                                  
|_http-title: Site doesn't have a title (text/html; charset=utf-8). 
27017/tcp open  mongodb MongoDB 4.2.9                                                                                                                                 
| mongodb-info:                                                                                                                                                       
|   MongoDB Build info                                                             
|     gitVersion = 06402114114ffc5146fd4b55402c96f1dc9ec4b5                        
|     bits = 64                                                                    
|     sysInfo = deprecated                                                                                                                                            
|     allocator = tcmalloc                                                                                                                                            
|     buildEnvironment                                                             
|       target_os = linux                                                                                                                                             
|       ccflags = -fno-omit-frame-pointer -fno-strict-aliasing -ggdb -pthread -Wall -Wsign-compare -Wno-unknown-pragmas -Winvalid-pch -Werror -O2 -Wno-unused-local-ty
pedefs -Wno-unused-function -Wno-deprecated-declarations -Wno-unused-const-variable -Wno-unused-but-set-variable -Wno-missing-braces -fstack-protector-strong -fno-bui
ltin-memcmp                                                                        
|       distmod = rhel70                                                                                                                                              
|       cxxflags = -Woverloaded-virtual -Wno-maybe-uninitialized -fsized-deallocation -std=c++17                                                                      
|       cc = /opt/mongodbtoolchain/v3/bin/gcc: gcc (GCC) 8.2.0                                                                                                        
|       target_arch = x86_64                                                                                                                                          
|       cxx = /opt/mongodbtoolchain/v3/bin/g++: g++ (GCC) 8.2.0                    
|       linkflags = -pthread -Wl,-z,now -rdynamic -Wl,--fatal-warnings -fstack-protector-strong -fuse-ld=gold -Wl,--build-id -Wl,--hash-style=gnu -Wl,-z,noexecstack -
Wl,--warn-execstack -Wl,-z,relro.
| mongodb-databases: 
|   ok = 1.0
|   totalSize = 307200.0
|   databases
|     0
|       name = account-app
|       sizeOnDisk = 131072.0
|       empty = false
|     3
|       name = local
|       sizeOnDisk = 73728.0
|       empty = false
|     2
|       name = config
|       sizeOnDisk = 61440.0
|       empty = false
|     1
|       name = admin
|       sizeOnDisk = 40960.0
|_      empty = false
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Dec 29 10:24:30 2022 -- 1 IP address (1 host up) scanned in 56.71 seconds
```

Node js
