![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f89ed107-9c60-4972-b2d3-a96ea103318e)

## First enumeration with nmap

```shell
# Nmap 7.94SVN scan initiated Wed Jul 10 22:48:16 2024 as: nmap -sCV -T4 -Pn -p 22,111,2049,39437,42675,44459,50985 -oN service.txt 10.150.150.59
Nmap scan report for 10.150.150.59
Host is up (0.15s latency).

PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 8.4p1 Ubuntu 5ubuntu1.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b3:f2:84:ff:e7:34:ab:88:d6:77:16:b9:a7:01:b6:a0 (RSA)
|   256 00:4f:42:d3:20:0c:b8:3b:81:20:d7:b7:1a:d2:df:65 (ECDSA)
|_  256 dc:e6:e6:e3:22:d8:b8:82:bd:97:46:3e:27:d9:a6:2e (ED25519)
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      34508/udp   mountd
|   100005  1,2,3      44459/tcp   mountd
|   100005  1,2,3      47131/udp6  mountd
|   100005  1,2,3      56261/tcp6  mountd
|   100021  1,3,4      37945/udp6  nlockmgr
|   100021  1,3,4      39437/tcp   nlockmgr
|   100021  1,3,4      40832/udp   nlockmgr
|   100021  1,3,4      42923/tcp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp  open  nfs      3-4 (RPC #100003)
39437/tcp open  nlockmgr 1-4 (RPC #100021)
42675/tcp open  mountd   1-3 (RPC #100005)
44459/tcp open  mountd   1-3 (RPC #100005)
50985/tcp open  mountd   1-3 (RPC #100005)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jul 10 22:48:29 2024 -- 1 IP address (1 host up) scanned in 13.15 seconds
```


## Enumerating NFS share pport 2049

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PwnTillDawn/10.150.150.59]
└─$ showmount -e 10.150.150.59                                                    
Export list for 10.150.150.59:
/nfsroot *
```
we can see the nfs share `/nsfroot`. let's mmount the share 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PwnTillDawn/10.150.150.59/mount]
└─$ sudo mount -t nfs 10.150.150.59:/nfsroot mnt 
                                                                                                                                                                       
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PwnTillDawn/10.150.150.59/mount]
└─$ ls -al mnt 
total 16
drwxr-xr-x 2 root root 4096 Dec 29  2023 .
drwxr-xr-x 2 root root 4096 Dec 29  2023 ..
-rwxrwxrwx 1 root root   41 Dec 29  2023 FLAG1
-rw-r--r-- 1 root root  616 Nov 17  2021 notes.txt
```
mounting the nfs share, we got a flag and note.txt


> checking the content of notes.txt

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PwnTillDawn/10.150.150.59]
└─$ cat notes.txt 
__INTERNAL PENTEST OF WEBAPP @ CLIENT01.PTD__

VULNS:
---

Logout via GET allowed

---

robots.txt -> check, admin page noted

---

SQLI in the main.php parameter id, time based:
?id=1-IF(MID(VERSION(),1,1) = '5', SLEEP(15), 0)

---

No serverside check of auth data, simply parsed from client-submitted string, checked with POC:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwic2VydmVyIjoiY2xpZW50MDEiLCJuYW1lIjoiamVzc3kiLCJwYXNzIjoiNz9KbnYyQjlARSR3IiwiaWF0IjoxNTE2MjM5MDIyfQ.H8XhbfR_rR33W8hkIWTWf8RtqUjFToA2RBMkCCDeKGM

---

X-Frame-Options + X-XSS-Protection headers not set

---

tocheck:

...
```


> Analyzing the jwt token

```js
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwic2VydmVyIjoiY2xpZW50MDEiLCJuYW1lIjoiamVzc3kiLCJwYXNzIjoiNz9KbnYyQjlARSR3IiwiaWF0IjoxNTE2MjM5MDIyfQ.H8XhbfR_rR33W8hkIWTWf8RtqUjFToA2RBMkCCDeKGM
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a604dbb6-343a-4088-9cf0-2a228eadf1f1)

using jwt.io do decode the jwt token, we can see username and password `jessy:7?Jnv2B9@E$w`


> Trying ssh with creds found

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/465210c7-cb84-45ee-819e-dbebe2048884)

login ssh successfully



## Privilege Escalation 

> Running linpeas

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bcb64cb8-a00e-4f00-914b-3f934d628539)

 Read the /etc/exports file, we can see the directory that is configured as no_root_squash, then we can access it from as a client and write inside that directory as if you were the local root of the machine.

> Refreshing on NFS dir permissions

- rw: This option gives the client computer both read and write access to the volume.
- sync: This option forces NFS to write changes to disk before replying. This results in a more stable and consistent environment but reduces the speed of file operations.
- inescure: This option allows clients to use any port to access NFS shares.
- no_subtree_check: This option prevents subtree checking, which is a process where the host must check whether the file is actually still available in the exported tree for every request.
- no_root_squash: This option allows privileged file writes inside the share. By default, NFS translates requests from a root user remotely into a non-privileged user on the server.


> Let's exploit this misconfiguration

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/57c02526-653d-4591-935d-22557e7b51ff)

create a dir on our target

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4fa1d9f8-5243-444c-a141-117f9c02d49c)

tried using the directory created but failed, so we used the same one we use to get the note.txt file

> Creating suid bash in the nfs folder 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9668db2c-5423-491b-b095-444e2cb4cd30)

create suid bash binary inside the nfs share directory 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d79ae20f-1b1d-40bc-99a1-90e3376e16a2)

back to jessy ssh and navigate to the `/nfsroot` directory, we can see the suid binary 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/aba5a083-6b74-416d-bdfe-d31df6d33d31)

got an error, the lib required is not found, let's try compile our own 


> creating malicious binary

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/fad51ce9-fde4-483a-8b17-13b98ab03028)

first download the target already made binary 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ab681b58-4cb9-42cc-a317-71cc6c28e1c0)

download into our attacker machine

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e50ded46-a6a8-4dfe-acb6-13b0059aaf2a)

make it suid and let's try again 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2a53b2d3-c4eb-410b-ab63-12bc64ad55c2)

try again and we are freaking root!!! 💀 easy box ain't it?

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0b9adbd6-7a69-45c3-a092-8f590f440e33)

root!!!


![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e5e83972-5a80-44b6-9094-3869bacb38c7)

we have our badge!
