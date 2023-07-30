![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/71b37b2e-e90f-4252-8fef-451b2a940875)![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2f91bf10-8203-4873-89d6-b90cb3f64953)

first enumeration with nmap 

```shell
# Nmap 7.94 scan initiated Sun Jul 30 17:12:19 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,135,139,445,5985,47001 -Pn 10.10.10.134
Nmap scan report for 10.10.10.134
Host is up (0.18s latency).

PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH for_Windows_7.9 (protocol 2.0)
| ssh-hostkey: 
|   2048 3a:56:ae:75:3c:78:0e:c8:56:4d:cb:1c:22:bf:45:8a (RSA)
|   256 cc:2e:56:ab:19:97:d5:bb:03:fb:82:cd:63:da:68:01 (ECDSA)
|_  256 93:5f:5d:aa:ca:9f:53:e7:f2:82:e6:64:a8:a3:a0:18 (ED25519)
135/tcp   open  msrpc       Microsoft Windows RPC
139/tcp   open  netbios-ssn Microsoft Windows netbios-ssn
445/tcp   open  Ö²pÓU      Windows Server 2016 Standard 14393 microsoft-ds
5985/tcp  open  http        Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http        Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Bastion
|   NetBIOS computer name: BASTION\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-07-30T18:12:38+02:00
| smb2-time: 
|   date: 2023-07-30T16:12:37
|_  start_date: 2023-07-30T16:03:20
|_clock-skew: mean: -39m57s, deviation: 1h09m13s, median: 0s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jul 30 17:12:46 2023 -- 1 IP address (1 host up) scanned in 27.69 seconds
```
cool let's enumerate smb 

### Enumerating SMB server 

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/HTB/Basiton]
└─$ smbclient -L 10.10.10.134 -N            
                                                                              
        Sharename       Type      Comment
        ---------       ----      -------      
        ADMIN$          Disk      Remote Admin
        Backups         Disk                                                  
        C$              Disk      Default share                                                                                                             
        IPC$            IPC       Remote IPC         
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.10.134 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available                                                                                                       
```
first we check for anonymous available shares, we can see the Backups share 

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/HTB/Basiton]
└─$ smbclient //10.10.10.134/Backups/                                          
Password for [WORKGROUP\n16hth4wk]:                                           
Try "help" to get a list of possible commands.                            
smb: \> dir                                                                   
  .                                   D        0  Tue Apr 16 11:02:11 2019
  ..                                  D        0  Tue Apr 16 11:02:11 2019
  note.txt                           AR      116  Tue Apr 16 11:10:09 2019
  SDT65CB.tmp                         A        0  Fri Feb 22 13:43:08 2019
  WindowsImageBackup                 Dn        0  Fri Feb 22 13:44:02 2019
                                       
                5638911 blocks of size 4096. 1179125 blocks available                                                                                       
smb: \> mget *         
Get file note.txt? y                                                                                                                                        
getting file \note.txt of size 116 as note.txt (0.2 KiloBytes/sec) (average 0.2 KiloBytes/sec)
Get file SDT65CB.tmp? y       
getting file \SDT65CB.tmp of size 0 as SDT65CB.tmp (0.0 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \> cd WindowsImageBackup                                                 
smb: \WindowsImageBackup\> dir
```
login the share and downloaded the `note.txt` file.



