first enumeration with nmap

```
# Nmap 7.93 scan initiated Tue May 16 07:40:40 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 80 -Pn 10.10.10.8
Nmap scan report for 10.10.10.8
Host is up (0.15s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue May 16 07:40:54 2023 -- 1 IP address (1 host up) scanned in 13.53 seconds
```
we have only one port available.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/21e41466-787a-4458-86d9-ed95b681df11)

opening the IP on a browser we got directed to a service running `HttpFileServer 2.3`. let's check for exploit.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/28a1a4d4-19ac-45a2-ad77-8accebe1e70b)

found an exploit on exploitdb. [exploit](https://www.exploit-db.com/exploits/49584)

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/20f5dd6f-244e-440a-888c-ef692a3a7bbf)

make some changes in the exploit. let's run it.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e22327bd-2512-42b5-8465-bf6267fe3bdd)

ran the exploit and boom got shell as `optimum\kostas`


## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/defea30f-a6a1-40e1-a47c-c569341bd6c3)

checking systeminfo, we can see the OS version is `6.3.9600 N/A Build 9600`, let's check for exploit online.






