![image](https://user-images.githubusercontent.com/87468669/194774642-06db820e-69da-4493-a93a-d01368bf8485.png)

```
You have been assigned to a client that wants a penetration test conducted on an environment due to be released to production in three weeks. 

Scope of Work

The client requests that an engineer conducts an external, web app, and internal assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:

    User.txt
    Root.txt

Additionally, the client has provided the following scope allowances:

    Ensure that you modify your hosts file to reflect internal.thm
    Any tools or techniques are permitted in this engagement
    Locate and note all vulnerabilities found
    Submit the flags discovered to the dashboard
    Only the IP address assigned to your machine is in scope

(Roleplay off)

I encourage you to approach this challenge as an actual penetration test. Consider writing a report, to include an executive summary, vulnerability and exploitation assessment, and remediation suggestions, as this will benefit you in preparation for the eLearnsecurity eCPPT or career as a penetration tester in the field.


Note - this room can be completed without Metasploit
```

First we start with nmap scan.

```
# Nmap 7.92 scan initiated Sun Oct  9 12:52:29 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 10.10.202.145
Warning: 10.10.202.145 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.10.202.145
Host is up (0.24s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE    SERVICE         VERSION
22/tcp   open     ssh             OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp   open     http            Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
1088/tcp filtered cplscrambler-al
1718/tcp filtered h323gatedisc
6129/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Oct  9 12:53:09 2022 -- 1 IP address (1 host up) scanned in 39.85 seconds
```

We have http and ssh ports open, let's enumerate http.

![image](https://user-images.githubusercontent.com/87468669/194774860-3a41d45e-101b-4a52-8952-bd414b525082.png)

cool it's running apache web service, let burst hidden directories

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Internal]
└─$ ffuf -u "http://10.10.202.145/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.202.145/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

blog                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 198ms]
index.html              [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 185ms]
javascript              [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 210ms]
phpmyadmin              [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 208ms]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 181ms]
wordpress               [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 200ms]
:: Progress: [4713/4713] :: Job [1/1] :: 225 req/sec :: Duration: [0:01:34] :: Errors: 80 ::
```

let's check the dir path /blog 

![image](https://user-images.githubusercontent.com/87468669/194775031-053e2b43-039f-4d02-8c05-4c69d6188775.png)

The page path /blog seems to be running wordpress cms, let's enumerate with wpscan.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Internal]                                                                                                              
└─$ wpscan --url http://10.10.202.145/blog/ --enumerate u  
```

Enumerating for usernames on the wordpress blog and we got only one which is `admin` .

![image](https://user-images.githubusercontent.com/87468669/194775325-5b1976e7-8db0-4bcf-8854-656c180c3fde.png)

Let's try bruteforce the user password for the user admin

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Internal]
└─$ wpscan --url http://10.10.202.145/blog/ --usernames admin --passwords /usr/share/wordlists/rockyou.txt 
```


