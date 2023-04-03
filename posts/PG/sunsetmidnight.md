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

notice a web server running on port 80, opening it on a browser redirected us to a vhost `sunset-midnight` and as we can see it is running a wordpress cms.




