---
layout: default
title : My OSCP cheat sheet
---

***
## Enumeration
***

```
nmap -sC -sV -o nmap -A -T5 <IP>
nmap -sC -vv -o nmap -T4 <IP>

Host Discovery
   •  nmap -sn 10.10.1.1-254 -vv -oA hosts
   •  netdiscover -r 10.10.10.0/24

DNS server discovery
   •  nmap -p 53 10.10.10.1-254 -vv -oA dcs
   
NSE Scripts Scan 
   * nmap -sV --script=vulscan/vulscan.nse (https://securitytrails.com/blog/nmap-vulnerability-scan)
   
Port specific NSE script list :

   ls /usr/share/nmap/scripts/ssh*
   ls /usr/share/nmap/scripts/smb*
   
```

***
## Scanning all ports
***

```
masscan -p1-65535,U:1-65535 --rate=1000 10.10.10.x -e tun0 > ports
ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')
nmap -Pn -sV -sC -p$ports 10.10.10.x

Running specific NSE scripts :
    nmap -Pn -sC -sV --script=vuln*.nse -p$ports 10.10.10.x -T4 -A
````
sC - default scripts, sV - scan for versions, oA- output all  formats
Optional - sT (performs full scan instead of syn-scan to prevent getting flagged by firewalls)
From Apache Version to finding Ubuntu version -> ubuntu httpd versions

***
## FTP: (port21)
***
anonymous login check 
   -  ftp <ip address>
   -  username : anonymous
   -  pwd : anonymous
   -  file upload -> put shell.php
   -  file download -> get file
  
Bruteforce ftp
   
  ```hydra -V -f -L <USERS_LIST> -P <PASSWORDS_LIST> ftp://<IP> -u -vV```
  
