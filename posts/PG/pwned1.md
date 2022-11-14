First we start with an nmap scan

`nmap -sC -sV -T4 -oN nmap.txt`

```
# Nmap 7.93 scan initiated Mon Nov 14 07:57:15 2022 as: nmap -sC -sV -T4 -oN quicknmap.txt -Pn 192.168.80.95
Nmap scan report for 192.168.80.95
Host is up (0.16s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 fecd90197491aef564a8a5e86f6eef7e (RSA)
|   256 813293bded9be798af2506795fde915d (ECDSA)
|_  256 dd72745d4d2da3623e81af0951e0144a (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Pwned....!!
|_http-server-header: Apache/2.4.38 (Debian)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Nov 14 07:57:42 2022 -- 1 IP address (1 host up) scanned in 26.50 seconds
```

great we have ftp, http and ssh service available, let's enumerate http first.

![image](https://user-images.githubusercontent.com/87468669/201595786-41d4b0f8-c865-46eb-81aa-4e03d533298e.png)

Let's check the source page... scroll to the bottom part of the page.

![image](https://user-images.githubusercontent.com/87468669/201596106-391800d1-dade-46cb-b6d1-74cd10b8f4e3.png)

let's burst hidden directories on the web page.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PG/pwned1]
└─$ ffuf -u "http://192.168.80.95/FUZZ" -w /usr/share/wordlists/dirb/common.txt -fc 403 -e .txt,.bak,.php 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.80.95/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .bak .php 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

                        [Status: 200, Size: 3065, Words: 1523, Lines: 76, Duration: 185ms]
index.html              [Status: 200, Size: 3065, Words: 1523, Lines: 76, Duration: 543ms]
robots.txt              [Status: 200, Size: 61, Words: 6, Lines: 6, Duration: 188ms]
:: Progress: [18456/18456] :: Job [1/1] :: 197 req/sec :: Duration: [0:03:15] :: Errors: 0 ::
```

let's check the robots.txt directory 

![image](https://user-images.githubusercontent.com/87468669/201596741-c262aab2-4021-4a70-8e75-9152b7df59a3.png)

we have two hidden directories path in the robots.txt directory... let's check the hidden_text directory 

![image](https://user-images.githubusercontent.com/87468669/201597393-27444777-e1ae-4747-8377-3e5baabed37c.png)

we got a file secret.dic, let's try read or download the file 

![image](https://user-images.githubusercontent.com/87468669/201597564-367386bd-4caa-4504-ad40-bcac2fbe4498.png)

we can see it is a directory wordlist, let's use it to fuzz further for hidden directory.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PG/pwned1]
└─$ ffuf -u "http://192.168.80.95FUZZ" -w secret.dic -fc 403 -e .txt,.bak,.php 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.80.95FUZZ
 :: Wordlist         : FUZZ: secret.dic
 :: Extensions       : .txt .bak .php 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

/pwned.vuln             [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 174ms]
                        [Status: 200, Size: 3065, Words: 1523, Lines: 76, Duration: 163ms]
:: Progress: [88/88] :: Job [1/1] :: 65 req/sec :: Duration: [0:00:06] :: Errors: 3 ::
```

we have another directory 'pwned.vuln', let's check it out...

![image](https://user-images.githubusercontent.com/87468669/201598430-a16cbf69-018d-4e9d-a2c2-2346ef59a41e.png)

we have a login page, let's check the source page...

![image](https://user-images.githubusercontent.com/87468669/201599083-0cecbd81-ecf1-4df6-92a8-c086cf3f0573.png)

we got ftp creds, let's try login ftp using those creds.

![image](https://user-images.githubusercontent.com/87468669/201599643-e4a2c275-77d1-420b-996b-fd7f66cea22b.png)

we are in ftp, there is a directory named share. entering the directory we got two files note.txt and an id_rsa key. let's download the files 

![image](https://user-images.githubusercontent.com/87468669/201599931-2f6b6c34-a37e-4bd5-afa7-bcd86059f5ea.png)

let's read the content of note.txt

![image](https://user-images.githubusercontent.com/87468669/201600131-97f45783-5203-431c-a83f-fd1fad69151c.png)

we can see a user named ariana, let's use the id_rsa key found earlier to login ssh using this username 

![image](https://user-images.githubusercontent.com/87468669/201600585-3ab89d73-b0e1-4a79-831e-2d99b43214cc.png)

Boom! we are in, now let's escalate privs. by running the `sudo -l` command

![image](https://user-images.githubusercontent.com/87468669/201600977-1aa4dfa9-7a5d-40d4-bc4b-caaccacf08d2.png)

we can see that User ariana may run the command above as user selena without password. let's try it out 

![image](https://user-images.githubusercontent.com/87468669/201601353-775bb3a5-98be-4b4f-b442-32a1cacadf2f.png)

after running that command, i observed we can run a command as user selena... let's exploit it 

![image](https://user-images.githubusercontent.com/87468669/201601860-6e8a3a21-0a20-4024-8cc3-8a5b4fdd0ac8.png)

running the above command dropped us in user selena shell, let's stabilize the shell. 

![image](https://user-images.githubusercontent.com/87468669/201602156-3fc318cb-ae38-44df-b7d2-1bea9f36352d.png)

Now we got a stable shell, let's get root 

