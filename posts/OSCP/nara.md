## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Wed Feb 28 14:04:28 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,88,135,139,389,445,464,593,636,3268,3269,5985,9389 -Pn 192.168.227.30
Nmap scan report for 192.168.227.30
Host is up (0.16s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-28 13:05:15Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: nara-security.com0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=Nara.nara-security.com
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:Nara.nara-security.com
| Not valid before: 2023-07-30T14:09:26
|_Not valid after:  2024-07-29T14:09:26
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: nara-security.com0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=Nara.nara-security.com
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:Nara.nara-security.com
| Not valid before: 2023-07-30T14:09:26
|_Not valid after:  2024-07-29T14:09:26
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: nara-security.com0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=Nara.nara-security.com
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:Nara.nara-security.com
| Not valid before: 2023-07-30T14:09:26
|_Not valid after:  2024-07-29T14:09:26
|_ssl-date: TLS randomness does not represent time
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: nara-security.com0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=Nara.nara-security.com
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:Nara.nara-security.com
| Not valid before: 2023-07-30T14:09:26
|_Not valid after:  2024-07-29T14:09:26
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Host: NARA; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-02-28T13:06:09
|_  start_date: N/A
|_clock-skew: 38s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Feb 28 14:06:08 2024 -- 1 IP address (1 host up) scanned in 100.36 seconds

```


## Enumerating smb 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/nana]                                                                                                                      
└─$ crackmapexec smb 192.168.227.30 -u anonymous -p '' --rid-brute                                                                                                        
SMB         192.168.227.30  445    NARA             [*] Windows 10.0 Build 20348 x64 (name:NARA) (domain:nara-security.com) (signing:True) (SMBv1:False)                  
SMB         192.168.227.30  445    NARA             [+] nara-security.com\anonymous:         
SMB         192.168.227.30  445    NARA             [-] Unexpected error with Neo4J                                                                                       
SMB         192.168.227.30  445    NARA             [-] Account not found on the domain                                                                                   
SMB         192.168.227.30  445    NARA             498: NARASEC\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         192.168.227.30  445    NARA             500: NARASEC\Administrator (SidTypeUser)                                                                              
SMB         192.168.227.30  445    NARA             501: NARASEC\Guest (SidTypeUser)           
SMB         192.168.227.30  445    NARA             502: NARASEC\krbtgt (SidTypeUser)     
SMB         192.168.227.30  445    NARA             512: NARASEC\Domain Admins (SidTypeGroup)                                                                             
SMB         192.168.227.30  445    NARA             513: NARASEC\Domain Users (SidTypeGroup)                                                                              
SMB         192.168.227.30  445    NARA             514: NARASEC\Domain Guests (SidTypeGroup)                                                                             
SMB         192.168.227.30  445    NARA             515: NARASEC\Domain Computers (SidTypeGroup)                      
SMB         192.168.227.30  445    NARA             516: NARASEC\Domain Controllers (SidTypeGroup)
SMB         192.168.227.30  445    NARA             517: NARASEC\Cert Publishers (SidTypeAlias)
SMB         192.168.227.30  445    NARA             518: NARASEC\Schema Admins (SidTypeGroup)  
SMB         192.168.227.30  445    NARA             519: NARASEC\Enterprise Admins (SidTypeGroup)                                                                         
SMB         192.168.227.30  445    NARA             520: NARASEC\Group Policy Creator Owners (SidTypeGroup)                                                               
SMB         192.168.227.30  445    NARA             521: NARASEC\Read-only Domain Controllers (SidTypeGroup)                                                              
SMB         192.168.227.30  445    NARA             522: NARASEC\Cloneable Domain Controllers (SidTypeGroup)                                                              
SMB         192.168.227.30  445    NARA             525: NARASEC\Protected Users (SidTypeGroup)
SMB         192.168.227.30  445    NARA             526: NARASEC\Key Admins (SidTypeGroup)     
SMB         192.168.227.30  445    NARA             527: NARASEC\Enterprise Key Admins (SidTypeGroup)                                                                     
SMB         192.168.227.30  445    NARA             553: NARASEC\RAS and IAS Servers (SidTypeAlias)                                                                       
SMB         192.168.227.30  445    NARA             571: NARASEC\Allowed RODC Password Replication Group (SidTypeAlias)                                                   
SMB         192.168.227.30  445    NARA             572: NARASEC\Denied RODC Password Replication Group (SidTypeAlias)                                                    
SMB         192.168.227.30  445    NARA             1000: NARASEC\NARA$ (SidTypeUser)      
SMB         192.168.227.30  445    NARA             1101: NARASEC\DnsAdmins (SidTypeAlias)    
SMB         192.168.227.30  445    NARA             1102: NARASEC\DnsUpdateProxy (SidTypeGroup)
SMB         192.168.227.30  445    NARA             1103: NARASEC\staff (SidTypeGroup)         
SMB         192.168.227.30  445    NARA             1104: NARASEC\Amelia.O'Brien (SidTypeUser)
SMB         192.168.227.30  445    NARA             1105: NARASEC\Damian.Johnson (SidTypeUser)
SMB         192.168.227.30  445    NARA             1106: NARASEC\Helen.Robinson (SidTypeUser)
SMB         192.168.227.30  445    NARA             1107: NARASEC\Sara.O'Sullivan (SidTypeUser)
SMB         192.168.227.30  445    NARA             1108: NARASEC\Jasmine.Roberts (SidTypeUser)
SMB         192.168.227.30  445    NARA             1109: NARASEC\Declan.Reynolds (SidTypeUser)
SMB         192.168.227.30  445    NARA             1110: NARASEC\Jodie.Summers (SidTypeUser)
SMB         192.168.227.30  445    NARA             1111: NARASEC\Carolyn.Hill (SidTypeUser)
SMB         192.168.227.30  445    NARA             1112: NARASEC\Jemma.Humphries (SidTypeUser)
SMB         192.168.227.30  445    NARA             1113: NARASEC\Tracy.White (SidTypeUser)
SMB         192.168.227.30  445    NARA             1115: NARASEC\Remote Access (SidTypeGroup)
SMB         192.168.227.30  445    NARA             1116: NARASEC\Enrollment (SidTypeGroup)
```
using crackmap-exec to perform `rid-brute` to get lists of usernames, let's save it in a file. 

```
staff 
Amelia.O'Brien 
Damian.Johnson 
Helen.Robinson 
Sara.O'Sullivan 
Jasmine.Roberts 
Declan.Reynolds 
Jodie.Summers 
Carolyn.Hill 
Jemma.Humphries 
Tracy.White 
Remote Access 
Enrollment 
```
made a user list. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/32900770-6efb-4a9e-8278-e076592c8874)

checking for anonymous;y available shares, we got nara share , and download the content in it. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/nana]
└─$ cat Important.txt 
Dear Team,

We hope this message finds you well. We wanted to remind all employees to take a moment each day to check the shared documents folder diligently. As part of our commitment to streamline processes and enhance efficiency, important documents are frequently uploaded to this folder for your attention and action.

The shared documents folder serves as a central hub for crucial updates, contracts, agreements, and various other essential materials requiring your attention. To ensure that you don't miss any critical information, please make it a habit to access the folder at the beginning of your workday or as often as possible.

Here are a few simple steps to stay up-to-date and ensure timely actions:

* Access the Shared Documents Folder: Log in to your company account and navigate to the designated shared documents folder. If you encounter any issues accessing the folder, please reach out to the IT department for assistance.

* Review New Additions: Look for any new documents that might have been uploaded since your last visit. These documents might require your signature, feedback, or acknowledgment.

* Take Action Promptly: If there are documents that need your attention, please act promptly and follow the necessary procedures as indicated within each document. Whether it's a signature, a comment, or any other form of response, timely actions are vital to keep our operations running smoothly.

* Seek Clarification: If you encounter any uncertainty or have questions about the documents you find, don't hesitate to reach out to the relevant department or the person mentioned in the document for clarification. It's essential that you fully understand what's required before proceeding.

Remember, staying informed and acting promptly ensures that projects progress seamlessly, contracts get executed on time, and the company as a whole operates efficiently. Your cooperation in this matter is greatly appreciated and contributes to our collective success.

Thank you for your attention to this matter, and if you have any concerns or suggestions to improve our document management process, please share them with your department head or the HR team.
```
checking the content of important.txt, it shows that users visits the shares regularly, and also we have a write access in he SMB share. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5a0e62ef-d8ef-423b-ac8b-e4f58995a883)

after some research found out to phish for ntlm-hash using this tool [ntlm_theft](https://github.com/Greenwolf/ntlm_theft). generate the payloads. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2b0c400a-4ffe-4bf4-a978-a00ad79bf525)

upload the lnk payload into each shares. after spawning `impacket-smbserver` 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8d8984dc-f326-4649-bb32-4faf9b2dc383)

check back the smb server, we captured hashes. 





