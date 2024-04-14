## First Enumeration with nmap

```shell
# Nmap 7.94SVN scan initiated Sun Apr 14 01:37:38 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80 -Pn 172.16.246.130
Nmap scan report for 172.16.246.130
Host is up (0.00021s latency).

PORT   STATE    SERVICE VERSION
22/tcp filtered ssh
80/tcp open     http    Apache httpd 2.4.38 ((Debian))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.38 (Debian)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Apr 14 01:37:46 2024 -- 1 IP address (1 host up) scanned in 8.09 seconds
```
we have only one port open. 


## Enumerating the web app 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/da0a7624-0fb4-4b77-b379-ac112d683e43)

opening the ip on a browser, we got a weird info sayin `your ansswer is your answer`.

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnyx/Responder]
└─$ ffuf -u 'http://172.16.246.130/FUZZ' -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -ic -e .php,.bak,.txt,.sql,.env,.sh,.phtml,.zip

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://172.16.246.130/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt
 :: Extensions       : .php .bak .txt .sql .env .sh .phtml .zip 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

                        [Status: 200, Size: 31, Words: 6, Lines: 2, Duration: 2ms]
.php                    [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 201ms]
.phtml                  [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 206ms]
                        [Status: 200, Size: 31, Words: 6, Lines: 2, Duration: 5ms]
.php                    [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 5ms]
.phtml                  [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 5ms]
filemanager.php         [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 54ms]
server-status           [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 7ms]
[WARN] Caught keyboard interrupt (Ctrl-C)
```

fuzzing for hidden dirs , we got a `filemanager.php`. Opening it on a browser, got redirected to the home page. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnyx/Responder]
└─$ ffuf -ic -u "http://responder/filemanager.php?FUZZ=/etc/hosts" -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -fs 0 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://responder/filemanager.php?FUZZ=/etc/hosts
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 0
________________________________________________

random                  [Status: 302, Size: 183, Words: 19, Lines: 8, Duration: 9ms]
:: Progress: [87651/87651] :: Job [1/1] :: 2679 req/sec :: Duration: [0:00:32] :: Errors: 0 ::
```
the param `filemanager.php` looks like a potential `LFI` endpoint. so fuzzing, we got the parameter. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/26ab9065-7b65-44dd-a010-105d3bbd3895)

using burpsuite to intercept request, we can see there's an LFI vulnerability present let's exploit the LFI to RCE




