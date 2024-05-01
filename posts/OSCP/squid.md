![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3de1754c-571a-4492-b2c3-6528d6bd2379)

## First Enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Wed May  1 01:29:59 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 135,139,445,3128 -Pn 192.168.201.189
Nmap scan report for 192.168.201.189
Host is up (0.16s latency).

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3128/tcp open  http-proxy    Squid http proxy 4.14
|_http-server-header: squid/4.14
|_http-title: ERROR: The requested URL could not be retrieved
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-05-01T00:32:40
|_  start_date: N/A
|_clock-skew: 2m20s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed May  1 01:30:58 2024 -- 1 IP address (1 host up) scanned in 59.05 seconds

```



