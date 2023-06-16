![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/406bef52-c8ba-4f03-8c11-f78603f09605)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Fri Jun 16 04:36:53 2023 as: nmap -sC -sV -oN normal.txt -p 22,2623,8089 -Pn 10.150.150.226
Nmap scan report for 10.150.150.226
Host is up (0.18s latency).

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 31484661d84e090ee050c35bb0f36b5e (RSA)
|   256 12bb91dab9ad18dd440b3b6cd63a9783 (ECDSA)
|_  256 1408ae353dba8c46e9f7cd49c9edb31e (ED25519)
2623/tcp open  http     Werkzeug httpd 0.14.1 (Python 2.7.15rc1)
|_http-server-header: Werkzeug/0.14.1 Python/2.7.15rc1
|_http-title: 404 Not Found
8089/tcp open  ssl/http Splunkd httpd
| http-robots.txt: 1 disallowed entry 
|_/
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
| Not valid before: 2019-10-25T09:41:05
|_Not valid after:  2022-10-24T09:41:05
|_http-server-header: Splunkd
|_http-title: splunkd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jun 16 04:37:40 2023 -- 1 IP address (1 host up) scanned in 47.08 seconds
```

