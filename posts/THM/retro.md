![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/66690e96-ad4c-452b-b759-1ec6e71b9cad)

## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Thu May  9 22:48:48 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 80,3389 -Pn 10.10.80.56
Nmap scan report for 10.10.80.56
Host is up (0.16s latency).

PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2024-05-09T21:51:35+00:00; +2m34s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: RETROWEB
|   NetBIOS_Domain_Name: RETROWEB
|   NetBIOS_Computer_Name: RETROWEB
|   DNS_Domain_Name: RetroWeb
|   DNS_Computer_Name: RetroWeb
|   Product_Version: 10.0.14393
|_  System_Time: 2024-05-09T21:51:31+00:00
| ssl-cert: Subject: commonName=RetroWeb
| Not valid before: 2024-05-08T21:45:36
|_Not valid after:  2024-11-07T21:45:36
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2m34s, deviation: 0s, median: 2m33s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu May  9 22:49:01 2024 -- 1 IP address (1 host up) scanned in 12.92 seconds
```


## Enumerating the web server 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/92f24fc2-40c9-45e2-9deb-abe1d5481d14)

Opening the ip on a browser, we got a windows default webpage

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/17bc7612-1aab-45bd-ae22-9c9b28f4274f)

bruteforcing web dirs, we got some dirs, and it also looks like a wordpress servce is running. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3a70e114-1030-4dc6-a6ca-c9034ad957a8)

navigating to the webpage, we can see a game server, looks like a web3 gaming site. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1ac439cd-3275-4438-8b4a-52d9a2553a21)

There's lots of information on the webpage, used cewl to make a wordlist from the webpage and we will try to bruteoforce later. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/57cac1be-9a11-4e5f-9739-d256bb87c7f8)

using wpscan to enumerate usernames and plugins present, we got user `wade`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bd963e93-b222-43bf-81e4-a0e3463c3ee2)

bruteforce password with the wordlist we made earlier from cewl. we got the password. `wade:parzival`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6e78d833-3376-4291-98b2-b47d1a4d904b)

login wp-admin. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6499e052-dff0-4d16-b797-c1711f752a2b)

login success to wp-admin. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/91313f81-f99d-4f93-9042-3e082132345e)

Upload a malicious php payload to the web app. in the theme selection option. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ba8f6eac-b331-4e44-8960-fd061e7c229c)

navigate to the webshell, and trigger RCE. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1492de65-4a4e-4c95-99db-e921b0a8567a)

Pop a revershell. 


## Privilege Escalation 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8903e1d8-2bae-4904-8e92-9a1b3eae1447)

SEIMPERSONATE is enabled. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/baae001b-4e4c-412c-8704-b295de572dee)

printspoofer and godpotato did not work. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0e903599-52ca-40ba-8102-3b8ed5a1277a)

trying cme to see if we can login RDP, we can see we can go in rdp. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/78e02863-8f33-4a1a-bd16-49b2cb5520e8)

login rdp to the target. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ea9166b9-b4db-40ff-a1e1-44f366e9ba81)

found a binary exe file in the recycle bin, restore it.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/aed59045-e5d2-4ad2-8664-6d23c69f56ae)

restore the file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0ee1100b-f4ca-42d4-a7fc-949a4beb1137)

try to run the application, it asked for admin password. we don't have it so let's move on.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7800eaa9-3a1b-416c-b927-5c8e3451c605)

checking systeminfo, we can see the build number of this machine. `10.0.14393 N/A Build 14393`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/546ad56d-25f5-445f-9e3c-cd8ffb714cb8)

found an exploit on github. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0d530565-10fa-41ad-8712-b0363ef672fd)

download the exploit to our machine. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d68eea23-6a75-4b55-8fcd-4dd080fc268a)

send the file to our target. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9ac57e5e-e841-4cc8-95f5-1a3096cf9f67)

extract thee content of the file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/092335d0-0909-4b68-b324-cd7072aebdfa)

run the exploit and boom we got Admin. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/23c0352c-c9f0-4fc6-97c7-52ba2a01264b)

and we are through. 

