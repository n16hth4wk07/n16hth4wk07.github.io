## First eumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Tue Feb 27 17:09:48 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 80,135,139,445,5040,8082,9092 -Pn 192.168.154.66
Nmap scan report for 192.168.154.66
Host is up (0.63s latency).

PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: H2 Database Engine (redirect)
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5040/tcp open  unknown
8082/tcp open  http          H2 database http console
|_http-title: H2 Console
9092/tcp open  XmlIpcRegSvc?
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 37s
| smb2-time: 
|   date: 2024-02-27T16:13:15
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Feb 27 17:12:58 2024 -- 1 IP address (1 host up) scanned in 189.71 seconds
```




