First enumeration with nmap 

`nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Sat Jan  7 09:19:56 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.202.134
Increasing send delay for 192.168.202.134 from 160 to 320 due to 33 out of 108 dropped probes since last increase.
Warning: 192.168.202.134 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.202.134
Host is up (0.15s latency).
Not shown: 65155 closed tcp ports (conn-refused), 378 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
13337/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Sat Jan  7 09:21:30 2023 -- 1 IP address (1 host up) scanned in 94.55 seconds
```

Now we know what ports are open, let's run default nmap scripts to know what services are runninn on those ports.

`nmap -sC -sV -oN normal.tcp -p 22,13337 -Pn <IP>`

```
# Nmap 7.93 scan initiated Sat Dec 31 18:10:26 2022 as: nmap -sC -sV -oN normal.tcp -p 22,13337 -Pn 192.168.202.134
Nmap scan report for 192.168.202.134
Host is up (0.18s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 74ba2023899262029fe73d3b83d4d96c (RSA)
|   256 548f79555ab03a695ad5723964fd074e (ECDSA)
|_  256 7f5d102762ba75e9bcc84fe27287d4e2 (ED25519)
13337/tcp open  http    Gunicorn 20.0.4
|_http-title: Remote Software Management API
|_http-server-header: gunicorn/20.0.4
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Dec 31 18:10:42 2022 -- 1 IP address (1 host up) scanned in 15.40 seconds
```

Now w know what services are running on those port, Let's start enumeration on port 13337 (http).

![image](https://user-images.githubusercontent.com/87468669/211141355-319a2b37-03f3-4c5a-8298-881c111812d6.png)

![image](https://user-images.githubusercontent.com/87468669/211141412-9f2539c0-2e5e-489c-b5fc-be6c0d5f7156.png)

We the request methods for each directories, let's check the `/logs` directory.

![image](https://user-images.githubusercontent.com/87468669/211141769-9cbcc3ae-f19d-49f3-815d-ec2e07fbdee4.png)

Got blocked by the WAF. let's intercept requests with burpsuite.



