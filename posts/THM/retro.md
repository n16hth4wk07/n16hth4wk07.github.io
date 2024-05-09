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





