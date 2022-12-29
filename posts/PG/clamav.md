First we start with an nmap scan 

`nmap -sC -sV -T4 -oN nmap.txt <IP>`

```
# Nmap 7.93 scan initiated Sun Dec 25 12:08:37 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 192.168.108.42                                                          
Warning: 192.168.108.42 giving up on port because retransmission cap hit (6).                                                                                         
Nmap scan report for 192.168.108.42                                                                                                                                   
Host is up (0.19s latency).                                                                                                                                           
Not shown: 992 closed tcp ports (conn-refused)                                                                                                                        
PORT     STATE    SERVICE     VERSION                                                                                                                                 
22/tcp   open     ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)                                                                                         
| ssh-hostkey:                                                                                                                                                        
|   1024 303ea4135f9a32c08e46eb26b35eee6d (DSA)                                                                                                                       
|_  1024 afa2493ed8f226124aa0b5ee6276b018 (RSA)
25/tcp   open     smtp        Sendmail 8.13.4/8.13.4/Debian-3sarge3
| smtp-commands: localhost.localdomain Hello [192.168.49.108], pleased to meet you, ENHANCEDSTATUSCODES, PIPELINING, EXPN, VERB, 8BITMIME, SIZE, DSN, ETRN, DELIVERBY,
 HELP
|_ 2.0.0 This is sendmail version 8.13.4 2.0.0 Topics: 2.0.0 HELO EHLO MAIL RCPT DATA 2.0.0 RSET NOOP QUIT HELP VRFY 2.0.0 EXPN VERB ETRN DSN AUTH 2.0.0 STARTTLS 2.0.0 For more info use "HELP <topic>". 2.0.0 To report bugs in the implementation send email to 2.0.0 sendmail-bugs@sendmail.org. 2.0.0 For local information send email 
to Postmaster at your site. 2.0.0 End of HELP info
80/tcp   open     http        Apache httpd 1.3.33 ((Debian GNU/Linux))
|_http-title: Ph33r
|_http-server-header: Apache/1.3.33 (Debian GNU/Linux)
| http-methods: 
|_  Potentially risky methods: TRACE
139/tcp  open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
199/tcp  open     smux        Linux SNMP multiplexer
445/tcp  open     netbios-ssn Samba smbd 3.0.14a-Debian (workgroup: WORKGROUP)
3011/tcp filtered trusted-web
6699/tcp filtered napster
Service Info: Host: localhost.localdomain; OSs: Linux, Unix; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 7h29m58s, deviation: 3h32m10s, median: 4h59m56s
|_nbstat: NetBIOS name: 0XBABE, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: share (dangerous)
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.14a-Debian)
|   NetBIOS computer name: 
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-12-25T11:09:41-05:00
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Dec 25 12:09:59 2022 -- 1 IP address (1 host up) scanned in 82.92 seconds
```

got some ports opened, checked port 80 first. Got nothing, so i enumerated port 25 smtp.

![image](https://user-images.githubusercontent.com/87468669/209990025-7d856819-36d8-487d-a9cc-6483dd359952.png)

Search for exploit using searchsploit and downloaded the exploit.

![image](https://user-images.githubusercontent.com/87468669/209990141-ac97f464-61bc-48a4-86d0-3a5a5d575ae1.png)

Ran the exploit against the target. The exploit attempt to bind a shell on port 31337 as root. Netc i nc directly to the IP on port 31337

![image](https://user-images.githubusercontent.com/87468669/209990350-d76adeb0-4d20-4077-bd72-23b0f7df320a.png)

Boom!!! got a root shell. And we are done🤠

