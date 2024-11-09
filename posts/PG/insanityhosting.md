First enumeration with nmap 

```shell
# Nmap 7.94 scan initiated Thu Aug 24 02:27:21 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,22,80 -Pn 192.168.211.124
Nmap scan report for 192.168.211.124
Host is up (0.24s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.45.241
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: ERROR
22/tcp open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 85:46:41:06:da:83:04:01:b0:e4:1f:9b:7e:8b:31:9f (RSA)
|   256 e4:9c:b1:f2:44:f1:f0:4b:c3:80:93:a9:5d:96:98:d3 (ECDSA)
|_  256 65:cf:b4:af:ad:86:56:ef:ae:8b:bf:f2:f0:d9:be:10 (ED25519)
80/tcp open  http    Apache httpd 2.4.6 ((CentOS) PHP/7.2.33)
|_http-server-header: Apache/2.4.6 (CentOS) PHP/7.2.33
|_http-title: Insanity - UK and European Servers
| http-methods: 
|_  Potentially risky methods: TRACE
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Aug 24 02:27:37 2023 -- 1 IP address (1 host up) scanned in 15.52 seconds
```

### Enumerating FTP (21)

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PG/Instanityhosting]
└─$ ftp 192.168.211.124
Connected to 192.168.211.124.
220 (vsFTPd 3.0.2)
Name (192.168.211.124:n16hth4wk): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -al
229 Entering Extended Passive Mode (|||33271|).
ftp: Can't connect to `192.168.211.124:33271': Connection timed out
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    3 0        0              17 Aug 16  2020 .
drwxr-xr-x    3 0        0              17 Aug 16  2020 ..
drwxr-xr-x    2 0        0               6 Apr 01  2020 pub
226 Directory send OK.
ftp> cd pub
250 Directory successfully changed.
ftp> ls -al
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        0               6 Apr 01  2020 .
drwxr-xr-x    3 0        0              17 Aug 16  2020 ..
226 Directory send OK.
ftp> put fulltcp.txt 
local: fulltcp.txt remote: fulltcp.txt
200 EPRT command successful. Consider using EPSV.
550 Permission denied.
ftp> 
ftp> cd ../
250 Directory successfully changed.
ftp> put fulltcp.txt 
local: fulltcp.txt remote: fulltcp.txt
200 EPRT command successful. Consider using EPSV.
550 Permission denied.
ftp> 
```
first enumeratng port 21, check if we could get any files or loot through ftp, we got nothing and we don't have file upload permission.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c46e7e1c-d83e-4237-a250-6fc75d5be7c7)

we noticed a web server running on port 80, opening it on a browser, we got a page talking about hosting servers. and also we can see a vhost name `insanityhosting.vm`. let's add it to our `/etc/passwd` file 


>fuzzing for hidden directories

![image](https://github.com/user-attachments/assets/46623299-d128-43d0-98fa-0bfcf665153f)

```
http://192.168.165.124/news/
http://192.168.165.124/monitoring/login.php
http://192.168.165.124/webmail/src/login.php
```

let's check out the dirs. 

![image](https://github.com/user-attachments/assets/445fe212-9673-427c-97fa-53c1fed68a95)

we got a user named `otis`, let's check around for more info.

![image](https://github.com/user-attachments/assets/89ca467a-f898-42d4-9104-1cacd1b63b51)

let's try bruteforce password 


![Uploading image.png…]()

login using username and password `otis:123456`.


