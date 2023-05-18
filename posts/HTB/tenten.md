First enumeration with nmap

```
# Nmap 7.93 scan initiated Thu May 18 08:25:16 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80 -Pn 10.10.10.10
Nmap scan report for tenten.htb (10.10.10.10)
Host is up (0.15s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ecf79d380c476ff0130fb93bd4d6e311 (RSA)
|   256 ccfe2de27fef4d41ae390e91ed7e9de7 (ECDSA)
|_  256 8db58318c07c5d3d38df4be1a4828a07 (ED25519)
80/tcp open  http    Apache httpd 2.4.18
|_http-generator: WordPress 4.7.3
|_http-title: Job Portal &#8211; Just another WordPress site
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu May 18 08:25:35 2023 -- 1 IP address (1 host up) scanned in 18.89 seconds
```
had `tenten.htb` to our `/etc/hosts` file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/73dadf93-5bf5-49c9-955a-5bed788bd52d)

noticed the web server running on port 80, opening the IP on a browser we got redirected to a wordpress service. let's enumerate using wpscan to check for vuln plugins and users.



