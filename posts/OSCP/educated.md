## First enumeration with nmap

```shell
# Nmap 7.94SVN scan initiated Tue Feb 20 15:05:34 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80 -Pn 192.168.239.13
Nmap scan report for 192.168.239.13
Host is up (0.13s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 98:4e:5d:e1:e6:97:29:6f:d9:e0:d4:82:a8:f6:4f:3f (RSA)
|   256 57:23:57:1f:fd:77:06:be:25:66:61:14:6d:ae:5e:98 (ECDSA)
|_  256 c7:9b:aa:d5:a6:33:35:91:34:1e:ef:cf:61:a8:30:1c (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Wisdom Elementary School
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Feb 20 15:05:46 2024 -- 1 IP address (1 host up) scanned in 11.60 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6aeecc46-9953-426f-929e-7c8b65b6c42c)

a web server is running on port `80`, openning it on a browser, we got a we school webpage. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Educated]
└─$ ffuf -ic -u "http://192.168.239.13/FUZZ" -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -fc 404,500,400 -e .txt,.php,.asp,.aspx,.old,.pd
f -fs 0                                                                       
                                                                              
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/        
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\       
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\        
          \/_/    \/_/   \/___/    \/_/         

       v1.5.0-dev         
________________________________________________ 
                                                                                                                                                            
 :: Method           : GET                                                    
 :: URL              : http://192.168.239.13/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
 :: Extensions       : .txt .php .asp .aspx .old .pdf 
 :: Follow redirects : false
 :: Calibration      : false                                                  
 :: Timeout          : 10                                                     
 :: Threads          : 40                                                     
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 404,500,400
 :: Filter           : Response size: 0                                                                                                                     
________________________________________________                                                                                                            
                                                                                                                                                            
.php                    [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 125ms]
                        [Status: 200, Size: 23698, Words: 7065, Lines: 559, Duration: 129ms]
assets                  [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 6187ms]
management              [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 124ms]
vendor                  [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 126ms]
```
fuzzing for hidden dirs, we got the above 


