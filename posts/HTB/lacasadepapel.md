![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/da15bcaa-96ff-420d-ae2d-931a404a7a17)

first enumeration with nmap

```
# Nmap 7.94 scan initiated Wed Jul  5 20:29:48 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,22,80,443 -Pn 10.10.10.131
Nmap scan report for 10.10.10.131
Host is up (0.23s latency).

PORT    STATE SERVICE  VERSION
21/tcp  open  ftp      vsftpd 2.3.4
22/tcp  open  ssh      OpenSSH 7.9 (protocol 2.0)
| ssh-hostkey: 
|   2048 03:e1:c2:c9:79:1c:a6:6b:51:34:8d:7a:c3:c7:c8:50 (RSA)
|   256 41:e4:95:a3:39:0b:25:f9:da:de:be:6a:dc:59:48:6d (ECDSA)
|_  256 30:0b:c6:66:2b:8f:5e:4f:26:28:75:0e:f5:b1:71:e4 (ED25519)
80/tcp  open  http     Node.js (Express middleware)
|_http-title: La Casa De Papel
443/tcp open  ssl/http Node.js Express framework
|_http-title: La Casa De Papel
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=lacasadepapel.htb/organizationName=La Casa De Papel
| Not valid before: 2019-01-27T08:35:30
|_Not valid after:  2029-01-24T08:35:30
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Server returned status 401 but no WWW-Authenticate header.
| tls-nextprotoneg: 
|   http/1.1
|_  http/1.0
| tls-alpn: 
|_  http/1.1
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jul  5 20:30:21 2023 -- 1 IP address (1 host up) scanned in 33.63 seconds
```
add `lacasadepapel.htb` to `/etc/hosts file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4c84dcbc-9bb4-46da-bd7b-54b6f023484e)

noticed a webserver running on port `80`, opening it on a browser, got directed to an homepage with qr code. playing around, got nothing useful.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d555e048-02b9-46e6-a40f-ca9514fb2d37)

moved to the next web server running on port `443`. opening it on a browser, got a certification error. tried fuzzing, got nothing.


### Enumerating FTP (port 21)

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/14a1178b-08a4-418c-87ac-91aa385b9012)

search for an exploit to the vsftp version `vsftpd 2.3.4` found a backdoor exploit on github. let's download and run this exploit.


