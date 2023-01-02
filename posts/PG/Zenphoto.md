First we start with nmap scan 

`nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Mon Jan  2 12:12:48 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.202.41
Increasing send delay for 192.168.202.41 from 80 to 160 due to 227 out of 755 dropped probes since last increase.
Increasing send delay for 192.168.202.41 from 160 to 320 due to 94 out of 312 dropped probes since last increase.
Increasing send delay for 192.168.202.41 from 320 to 640 due to 70 out of 232 dropped probes since last increase.
Nmap scan report for 192.168.202.41
Host is up (0.22s latency).
Not shown: 65531 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
3306/tcp open  mysql

Read data files from: /usr/bin/../share/nmap
# Nmap done at Mon Jan  2 12:14:20 2023 -- 1 IP address (1 host up) scanned in 91.93 seconds
```

Great we have some ports open, let's run nmap default scripts scan to know what services are on those ports.

`nmap -sC -sV -oN normal.tcp -p 22,23,80,3306 -Pn <IP>`

```
# Nmap 7.93 scan initiated Mon Jan  2 13:30:48 2023 as: nmap -sC -sV -oN normal.tcp -p 22,23,80,3306 -Pn 192.168.202.41
Nmap scan report for 192.168.202.41
Host is up (0.80s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 5.3p1 Debian 3ubuntu7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 8392abf2b76e27087ba9b872328ccc29 (DSA)
|_  2048 6577fa50fd4d9ef167e5cc0cc696f23e (RSA)
23/tcp   open  ipp     CUPS 1.4
|_http-title: 403 Forbidden
|_http-server-header: CUPS/1.4
| http-methods: 
|_  Potentially risky methods: PUT
80/tcp   open  http    Apache httpd 2.2.14 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.2.14 (Ubuntu)
3306/tcp open  mysql   MySQL (unauthorized)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Jan  2 13:31:46 2023 -- 1 IP address (1 host up) scanned in 58.67 seconds
```

Nice we now know what service are open, let's start by enumerating port 80 first.

![image](https://user-images.githubusercontent.com/87468669/210232197-1b96c0b0-1fa9-4f3c-b01f-c484ac5de1cf.png)

Checked the default home page, it's under construction, i also checked page source, i got nothing. Let's burst hidden directories using ffuf.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Zenphoto]
└─$ ffuf -u "http://192.168.202.41/FUZZ" -w /usr/share/wordlists/dirb/common.txt -fc 403,500 -fs 0,5 -e .txt,.php,.xml,.bak

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.202.41/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .php .xml .bak 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403,500
 :: Filter           : Response size: 0,5
________________________________________________

                        [Status: 200, Size: 75, Words: 2, Lines: 5, Duration: 224ms]
index                   [Status: 200, Size: 75, Words: 2, Lines: 5, Duration: 208ms]
index.html              [Status: 200, Size: 75, Words: 2, Lines: 5, Duration: 207ms]
test                    [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 234ms]
:: Progress: [23070/23070] :: Job [1/1] :: 160 req/sec :: Duration: [0:02:27] :: Errors: 0 ::
```

Now we have the hidden dirs let's check the content of /test.

![image](https://user-images.githubusercontent.com/87468669/210234780-a345c73c-3527-406f-83b5-5cc654e453eb.png)

As we can see the web server is running zenophoto cms. Let's check the page source for more info about this target.

![image](https://user-images.githubusercontent.com/87468669/210234936-d03f1304-e7d9-4f67-b53b-e17110813e7c.png)

As we can see this targte is running `zenphoto 1.4.1.4`, let's check for public exploit.

![image](https://user-images.githubusercontent.com/87468669/210235212-61dd78ea-5db7-43b4-bd09-c793dbb785a8.png)

Nice we found a public RCE exploit. Let's run the exploit.

![image](https://user-images.githubusercontent.com/87468669/210235535-e7aaf29a-2a2c-4776-bcf9-4a73a77272bc.png)

Running the exploit gave us an RCE and was able to get a rev shell... Now let's escalate privs 

```
uname -a && cat /etc/issue
find / -perm -u=s -type f 2>/dev/null
```

![image](https://user-images.githubusercontent.com/87468669/210238633-4e5b6377-a720-4f8a-8609-e4b5fcbcf399.png)

We have pkexec available in suid, let's run pwnkit kernel exploit.

![image](https://user-images.githubusercontent.com/87468669/210238702-9c51968d-e41d-4eb1-8208-bf8e9a55f169.png)

Found an exploit on github, cloned the repo.

![image](https://user-images.githubusercontent.com/87468669/210238944-5ae960f3-6cb0-464a-8825-f6efd0c5fbec.png)

Use wget to download the exploit from attacker machine to target and compiled the exploit.

![image](https://user-images.githubusercontent.com/87468669/210239147-18475761-0b39-4ec6-b25a-94304329dea3.png)

Boom and we are root...🤠😎

