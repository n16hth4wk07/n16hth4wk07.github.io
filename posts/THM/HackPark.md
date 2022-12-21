![image](https://user-images.githubusercontent.com/87468669/208858721-852ee5c2-b27e-4d83-a29c-a0c56513edfa.png)

First we start with nmap scan

`nmap -sC -sV -T4 -oN nmap.txt <IP`

```
# Nmap 7.93 scan initiated Tue Dec 20 17:26:57 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 10.10.213.54
Nmap scan report for 10.10.213.54
Host is up (0.16s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT     STATE SERVICE            VERSION
80/tcp   open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-IIS/8.5
|_http-title: hackpark | hackpark amusements
3389/tcp open  ssl/ms-wbt-server?
|_ssl-date: 2022-12-20T16:28:45+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: HACKPARK
|   NetBIOS_Domain_Name: HACKPARK
|   NetBIOS_Computer_Name: HACKPARK
|   DNS_Domain_Name: hackpark
|   DNS_Computer_Name: hackpark
|   Product_Version: 6.3.9600
|_  System_Time: 2022-12-20T16:28:42+00:00
| ssl-cert: Subject: commonName=hackpark
| Not valid before: 2022-12-19T16:25:26
|_Not valid after:  2023-06-20T16:25:26
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Dec 20 17:28:46 2022 -- 1 IP address (1 host up) scanned in 108.50 seconds
```

Nice we got port 80 http and port 3389. Enumerating port 80 first. Checking the IP on browser.

![image](https://user-images.githubusercontent.com/87468669/208859381-9cac2b09-83ab-4ad8-ac6f-ec8ff17f0dd8.png)
 
we got the default home page, just a greeting from the clown penny wise... check the source page.

![image](https://user-images.githubusercontent.com/87468669/208860000-2a54e0ec-54ea-4080-94d9-48f4f2d73576.png)

Checking the source page we can see it is running a blogengine 3.3.6.0 cms... Bursting hidden directories next

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/HackPark]                                                                                                       [10/11]
└─$ ffuf -u "http://10.10.155.116/FUZZ" -w /usr/share/wordlists/dirb/common.txt -fc 403,500 -e .txt,.php,.zip,.bak                                                    
                                                                                                                                                                      
        /'___\  /'___\           /'___\                                                                                                                               
       /\ \__/ /\ \__/  __  __  /\ \__/                                                                                                                               
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\                                                                                                                              
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/                                                                                                                              
         \ \_\   \ \_\  \ \____/  \ \_\                                                                                                                               
          \/_/    \/_/   \/___/    \/_/                                                                                                                               
                                                                                                                                                                      
       v1.5.0 Kali Exclusive <3                                                                                                                                       
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.155.116/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .php .zip .bak 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403,500
________________________________________________

                        [Status: 200, Size: 8997, Words: 1588, Lines: 157, Duration: 233ms]
account                 [Status: 301, Size: 152, Words: 9, Lines: 2, Duration: 210ms]
ADMIN                   [Status: 302, Size: 173, Words: 6, Lines: 4, Duration: 2584ms]
Admin                   [Status: 302, Size: 173, Words: 6, Lines: 4, Duration: 2649ms]
admin                   [Status: 302, Size: 173, Words: 6, Lines: 4, Duration: 2722ms]
archive                 [Status: 200, Size: 8325, Words: 1413, Lines: 148, Duration: 258ms]
Archive                 [Status: 200, Size: 8325, Words: 1413, Lines: 148, Duration: 214ms]
archives                [Status: 200, Size: 8326, Words: 1413, Lines: 148, Duration: 233ms]
aspnet_client           [Status: 301, Size: 158, Words: 9, Lines: 2, Duration: 215ms]
contacto                [Status: 200, Size: 9936, Words: 1725, Lines: 166, Duration: 3037ms]
contact                 [Status: 200, Size: 9935, Words: 1725, Lines: 166, Duration: 3232ms]
:: Progress: [23070/23070] :: Job [1/1] :: 182 req/sec :: Duration: [0:03:11] :: Errors: 0 ::

```

checking the /admin directory first.

![image](https://user-images.githubusercontent.com/87468669/208860971-09695e3a-eefa-458e-b304-7db188f0c578.png)

we have a login page. Try to bypass the login page but failed... Let's bruteforce the login page use hydra. First we need to capture the requests using burp suite.

![image](https://user-images.githubusercontent.com/87468669/208863944-399f9108-af59-48b0-9b12-c82ff6d89d65.png)




