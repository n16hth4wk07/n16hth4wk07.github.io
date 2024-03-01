## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Fri Mar  1 15:21:40 2024 as: nmap -sC -sV -T4 -oN normal.txt -p 80,135,445,49666 -Pn 192.168.227.188
Nmap scan report for 192.168.227.188
Host is up (0.13s latency).

PORT      STATE SERVICE       VERSION
80/tcp    open  http          Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
|_http-title: Craft
135/tcp   open  msrpc         Microsoft Windows RPC
445/tcp   open  microsoft-ds?
49666/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-03-01T14:23:22
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: 40s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar  1 15:23:20 2024 -- 1 IP address (1 host up) scanned in 99.51 seconds
```

## Enumerating Port 80

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ca04a717-bf45-4c93-8020-108b0460bc10)




