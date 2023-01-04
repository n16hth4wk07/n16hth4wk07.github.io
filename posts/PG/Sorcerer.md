First Enumeration with nmap

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v <IP> -Pn`

```
# Nmap 7.93 scan initiated Wed Jan  4 21:12:11 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.59.100
Increasing send delay for 192.168.59.100 from 0 to 5 due to 18 out of 58 dropped probes since last increase.
Increasing send delay for 192.168.59.100 from 5 to 10 due to 11 out of 26 dropped probes since last increase.
Increasing send delay for 192.168.59.100 from 10 to 20 due to 11 out of 29 dropped probes since last increase.
Increasing send delay for 192.168.59.100 from 80 to 160 due to 11 out of 22 dropped probes since last increase.
Warning: 192.168.59.100 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.59.100
Host is up (0.25s latency).
Not shown: 40736 closed tcp ports (conn-refused), 24792 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
111/tcp   open  rpcbind
7742/tcp  open  msss
8080/tcp  open  http-proxy

Read data files from: /usr/bin/../share/nmap
# Nmap done at Wed Jan  4 21:13:58 2023 -- 1 IP address (1 host up) scanned in 106.67 seconds
```

Cool we got some ports available, let's run default nmap scripts to know what service are running on those ports 

`nmap -sC -sV -oN normal.tcp -p 22,80,111,2049,7742,8080 -Pn <IP>`

```
# Nmap 7.93 scan initiated Wed Jan  4 21:17:52 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80,111,2049,7742,8080 -Pn 192.168.59.100
Nmap scan report for 192.168.59.100                                                
Host is up (0.54s latency).
                                                                                   
PORT     STATE SERVICE VERSION                                                     
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:              
|   2048 812a4224b590a1ce9bace74e1d6db4c6 (RSA)     
|   256 d0732a05527f89093776e356c8ab2099 (ECDSA)
|_  256 3a2dde33b01ef2350f8dc8d78ff9e00e (ED25519)
80/tcp   open  http    nginx                                                       
|_http-title: Site doesn't have a title (text/html).
111/tcp  open  rpcbind 2-4 (RPC #100000)                                           
| rpcinfo:                            
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100003  3           2049/udp   nfs                                             
|   100003  3,4         2049/tcp   nfs                                             
|   100005  1,2,3      42865/tcp   mountd 
|   100005  1,2,3      43373/udp   mountd 
|   100021  1,3,4      39859/tcp   nlockmgr
|   100021  1,3,4      43044/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/udp   nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
7742/tcp open  http    nginx     
|_http-title: SORCERER                                                             
8080/tcp open  http    Apache Tomcat 7.0.4             
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/7.0.4                                                                                                                                      
|_http-open-proxy: Proxy might be redirecting requests                                                                                                                 
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
                                                                                   
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jan  4 21:18:28 2023 -- 1 IP address (1 host up) scanned in 35.89 seconds
```

cool we now know what services are running on those ports. Let's start by enumerating port 111 (RPC) nfs.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Sorcerer]
└─$ showmount -e 192.168.218.100
Export list for 192.168.218.100:
```

checked the nfs service to see if we can get any nfs share. Next let's enumerate port 7742.

