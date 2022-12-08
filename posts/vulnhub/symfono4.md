first we start with nmap scan

`nmap -sC -sV -T4 -oN nmap.txt <IP>`

```
# Nmap 7.93 scan initiated Thu Dec  8 11:11:38 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 192.168.0.102
Nmap scan report for 192.168.0.102
Host is up (0.00040s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10 (protocol 2.0)
| ssh-hostkey: 
|   2048 f9c17395a417dff6ed5c8e8ac805f98f (RSA)
|   256 bec1fdf13364399a683564f9bd27ec01 (ECDSA)
|_  256 66f76ae8edd51d2d36326439384f9c8a (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.38 (Debian)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Dec  8 11:11:45 2022 -- 1 IP address (1 host up) scanned in 7.77 seconds

```

Nice we can see the open ports available which are port 22 (ssh) and port 80 (http). Let's enumerate port 80 first. By checking the IP on browser.

![image](https://user-images.githubusercontent.com/87468669/206421089-ebf8b924-dc0a-44ab-8e89-6cfedb861ea5.png)

We can see the default webpage running, let's view source page.

![image](https://user-images.githubusercontent.com/87468669/206422077-119d34a7-6bb0-45b4-a220-69b44e5bd354.png)

The source page is empty, Let’s burst hidden directory using a fuzzer aka sharingan 👀 this time mangekyo sharingan 😂. 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/Vulnhub/symfonos4]
└─$ ffuf -u "http://192.168.0.102/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -fc 403 -e .bak,.php,.txt,.zip -fs 201

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.0.102/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
 :: Extensions       : .bak .php .txt .zip 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
 :: Filter           : Response size: 201
________________________________________________

css                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 7ms]
manual                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 7ms]
js                      [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 7ms]
javascript              [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 7ms]
sea.php                 [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 69ms]
atlantis.php            [Status: 200, Size: 1718, Words: 765, Lines: 44, Duration: 133ms]
gods                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 72ms]
:: Progress: [1102800/1102800] :: Job [1/1] :: 2077 req/sec :: Duration: [0:04:44] :: Errors: 0 ::

```

Checking sea.php redirects to atlantis.php which is a login page.

![image](https://user-images.githubusercontent.com/87468669/206423321-81256bcd-49ef-4e55-8341-41657294f324.png)

Let's check the other directory "gods"

![image](https://user-images.githubusercontent.com/87468669/206423607-170bfccb-2e62-4e36-8d6f-bcde3616d986.png)

As we can see we got some usernames log file, let's check em... 
