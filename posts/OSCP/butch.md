## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Sat Mar  9 21:57:09 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 21,25,135,139,445,450,5985 -Pn 192.168.220.63
Nmap scan report for 192.168.220.63
Host is up (0.15s latency).

PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
25/tcp   open  smtp          Microsoft ESMTP 10.0.17763.1
| smtp-commands: butch Hello [192.168.45.180], TURN, SIZE 2097152, ETRN, PIPELINING, DSN, ENHANCEDSTATUSCODES, 8bitmime, BINARYMIME, CHUNKING, VRFY, OK
|_ This server supports the following commands: HELO EHLO STARTTLS RCPT DATA RSET MAIL QUIT HELP AUTH TURN ETRN BDAT VRFY
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
450/tcp  open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Butch
| http-methods: 
|_  Potentially risky methods: TRACE
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: butch; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 51s
| smb2-time: 
|   date: 2024-03-09T20:58:19
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Mar  9 21:58:08 2024 -- 1 IP address (1 host up) scanned in 59.03 seconds
```


## Enumerating port 450

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a4eb4bed-8639-4d74-a5f9-5913c615ea74)

there's a web server running on port `450` opening it on a browser, we got redirected to a webpage asking us to login. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/aa4b76bf-a924-420a-bc87-1062f1de6689)

tried sql injection payload `admin' or 1=1#` to bypass auth, we got an mssql error. I've exploited a vuln like this to trigger RCE. let's try our luck

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/062735a0-7677-4ae5-a478-69c12b9d3e14)

confirm it is a blind sql. tried to exploit to get RCE, found out the user can exec the `xp_cmdshell`. 





