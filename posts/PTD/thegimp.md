![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/96ef3d60-3c07-481a-821b-ded07b3fa995)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Tue May 30 11:13:47 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 80,135,443,8765 -Pn 10.150.150.201
Nmap scan report for thegimp.pwntilldawn.local (10.150.150.201)
Host is up (0.15s latency).

PORT     STATE SERVICE  VERSION
80/tcp   open  http     Microsoft IIS httpd 8.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/8.5
|_http-title: IIS Windows Server
135/tcp  open  msrpc    Microsoft Windows RPC
443/tcp  open  ssl/http Microsoft IIS httpd 8.5
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/8.5
|_ssl-date: 2021-07-02T14:02:00+00:00; -1y331d20h12m17s from scanner time.
| http-methods: 
|_  Potentially risky methods: TRACE
| ssl-cert: Subject: commonName=thegimp.pwntilldawn.local
| Not valid before: 2020-05-21T14:10:28
|_Not valid after:  2020-11-20T14:10:28
8765/tcp open  http     Microsoft IIS httpd 8.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/8.5
|_http-title: The Gimps' House
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: -696d20h12m17s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue May 30 11:14:17 2023 -- 1 IP address (1 host up) scanned in 30.47 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/978bd534-2c78-4154-8f07-22237b49f8cf)

noticed a web server running on port `8765`, opening it on a browsr, got redirected to a login page. checked the page source, nothing useful so let's try bypass the login page.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d34236a3-e7a1-44a4-9b1c-28a9cf69298b)

trying sqli auth bypass payload `admin' or '1'='1'--:admin' or '1'='1'--` worked, got admin page. let's play around.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/73844968-28ff-4181-9891-e6bed0845b24)

scrolling down a little, we can see some credentials. `pwntilldawn\thegimp:Mhmmhhmmmhhh###1`. looks like RDP
