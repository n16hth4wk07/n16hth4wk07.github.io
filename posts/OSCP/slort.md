## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Fri Feb 23 11:05:05 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 21,135,139,445,3306,4443,5040,7680,8080 -Pn 192.168.194.53
Nmap scan report for 192.168.194.53
Host is up (0.15s latency).

PORT     STATE  SERVICE       VERSION
21/tcp   open   ftp           FileZilla ftpd 0.9.41 beta
135/tcp  open   msrpc         Microsoft Windows RPC
139/tcp  open   netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open   microsoft-ds?
3306/tcp open   mysql?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPBindReq, LDAPSearchReq, LPDString, NULL, RPCCheck, RTSPRequest, TLSSessionReq, TerminalServerCookie: 
|_    Host '192.168.45.171' is not allowed to connect to this MariaDB server
4443/tcp open   http          Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
5040/tcp open   unknown
7680/tcp closed pando-pub
8080/tcp open   http          Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Feb 23 11:09:41 2024 -- 1 IP address (1 host up) scanned in 275.14 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8ef27fa7-b28e-4dcc-a16e-8baa76c3335c)

there's a web server running on port `8080`, opening it on a browser, we got redirected to an `xampp` web page. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Slort]
└─$ ffuf -ic -u "http://192.168.194.53:8080/FUZZ" -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -fc 404,500,400 -e .txt,.php,.asp,.aspx,.old,.pdf -fs 0 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.194.53:8080/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
 :: Extensions       : .txt .php .asp .aspx .old .pdf 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 0
 :: Filter           : Response status: 404,500,400
________________________________________________

img                     [Status: 301, Size: 345, Words: 22, Lines: 10, Duration: 148ms]
site                    [Status: 301, Size: 346, Words: 22, Lines: 10, Duration: 135ms]
licenses                [Status: 403, Size: 1205, Words: 127, Lines: 46, Duration: 139ms]
dashboard               [Status: 301, Size: 351, Words: 22, Lines: 10, Duration: 140ms]
%20                     [Status: 403, Size: 1046, Words: 102, Lines: 43, Duration: 141ms]
[WARN] Caught keyboard interrupt (Ctrl-C)
```


