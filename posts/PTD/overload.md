![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/725066a0-43a2-4757-9a2b-06ed3ca6fb62)

## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Thu Apr  4 17:24:31 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 21,22,25,53,389,33060 -Pn 10.150.150.17
Nmap scan report for 10.150.150.17
Host is up (0.25s latency).

PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f9:30:f8:ef:be:da:2d:42:0d:34:ee:21:4b:2f:4a:8e (RSA)
|   256 fd:f4:aa:82:76:9a:5d:6a:c2:9b:ad:e7:04:db:82:0e (ECDSA)
|_  256 4b:b4:6f:90:1a:f2:76:38:3c:de:04:50:83:e7:55:f0 (ED25519)
25/tcp    open  smtp    Postfix smtpd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=overload
| Subject Alternative Name: DNS:overload
| Not valid before: 2020-08-01T15:05:31
|_Not valid after:  2030-07-30T15:05:31
|_smtp-commands: overload.fritz.box, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
53/tcp    open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
389/tcp   open  ldap    OpenLDAP 2.2.X - 2.3.X
33060/tcp open  mysqlx?
| fingerprint-strings: 
|   NotesRPC, X11Probe: 
|     Invalid message"
|_    HY000
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
Service Info: Host:  overload.fritz.box; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Apr  4 17:26:27 2024 -- 1 IP address (1 host up) scanned in 115.91 seconds
```


## Enumerating web server 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/17d833ce-34ba-4df4-848f-768f890184e1)

checking the ip on a browser, we got a vhost. add `overload.ptd` to `/etc/hosts`. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PTD/overload]
└─$ ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.overload.ptd" -u http://10.150.150.17/ -fc 403,401 -fs 13 
                                                                              
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\       
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/       
         \ \_\   \ \_\  \ \____/  \ \_\        
          \/_/    \/_/   \/___/    \/_/        
                                                                              
       v1.5.0-dev
________________________________________________
                                                                              
 :: Method           : GET                                                                                                                                  
 :: URL              : http://10.150.150.17/  
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
 :: Header           : Host: FUZZ.overload.ptd
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10                                                     
 :: Threads          : 40                                                     
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403,401 
 :: Filter           : Response size: 13
________________________________________________                                                                                                            
                                                                                                                                                            
shop                    [Status: 200, Size: 24769, Words: 4444, Lines: 364, Duration: 493ms] 
cms                     [Status: 200, Size: 14143, Words: 5680, Lines: 329, Duration: 8045ms]
wordpress               [Status: 200, Size: 25966, Words: 1204, Lines: 346, Duration: 4134ms]
drupal                  [Status: 200, Size: 8822, Words: 1512, Lines: 232, Duration: 2655ms]
database                [Status: 200, Size: 14762, Words: 2348, Lines: 221, Duration: 2054ms]

```
fuzzing for sub domains. 



