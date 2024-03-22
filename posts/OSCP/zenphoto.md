## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Fri Mar 22 00:20:47 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,23,80,3306 -Pn 192.168.190.41
Nmap scan report for 192.168.190.41
Host is up (0.17s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 5.3p1 Debian 3ubuntu7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 83:92:ab:f2:b7:6e:27:08:7b:a9:b8:72:32:8c:cc:29 (DSA)
|_  2048 65:77:fa:50:fd:4d:9e:f1:67:e5:cc:0c:c6:96:f2:3e (RSA)
23/tcp   open  ipp     CUPS 1.4
|_http-server-header: CUPS/1.4
| http-methods: 
|_  Potentially risky methods: PUT
|_http-title: 403 Forbidden
80/tcp   open  http    Apache httpd 2.2.14 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.2.14 (Ubuntu)
3306/tcp open  mysql   MySQL (unauthorized)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar 22 00:21:26 2024 -- 1 IP address (1 host up) scanned in 39.73 seconds
```

## Enumerating web server 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/456ef617-a6fc-4a75-8b47-7090d8bd3f6c)

a web server is running on port `80`, opening it on a browser, we got an `under construction` response on the webpage. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/cc3edf23-206f-4d19-8e35-072d2e569b27)

fuzzing for hidden dir, we can see dir `/test`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/96143528-cae5-42c2-b13e-04d0c0618288)

navigating to the dir, we ca see it is running an image service `Zenphoto`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a15ce474-9911-4129-8d25-1c5e063878cf)

checking the source code of the web page, we can see the version of the service. `1.4.1.4`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/48ec989e-c9b9-42cf-9806-9169372c214d)

found an exploit on searcsploit. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/21ed8358-0032-4f1d-b226-ba8fdc4d1d09)

run the exploit and we got RCE. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f35251ea-1dc8-49e9-8088-f97f5747796b)

popped a reverse shell.



## Privilege Escalation 





