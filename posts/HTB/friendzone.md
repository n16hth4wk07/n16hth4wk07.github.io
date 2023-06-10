![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4bdb62bf-8e94-4264-a0d6-d2a409a90826)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Sat Jun 10 21:47:27 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,22,80,53,139,443,445 -Pn 10.10.10.123
Nmap scan report for 10.10.10.123
Host is up (0.15s latency).

PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 3.0.3
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 a96824bc971f1e54a58045e74cd9aaa0 (RSA)
|   256 e5440146ee7abb7ce91acb14999e2b8e (ECDSA)
|_  256 004e1a4f33e8a0de86a6e42a5f84612b (ED25519)
53/tcp  open  domain      ISC BIND 9.11.3-1ubuntu1.2 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.11.3-1ubuntu1.2-Ubuntu
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Friend Zone Escape software
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp open  ssl/http    Apache httpd 2.4.29
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.29 (Ubuntu)
| ssl-cert: Subject: commonName=friendzone.red/organizationName=CODERED/stateOrProvinceName=CODERED/countryName=JO
| Not valid before: 2018-10-05T21:02:30
|_Not valid after:  2018-11-04T21:02:30
| tls-alpn: 
|_  http/1.1
|_http-title: 400 Bad Request
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Hosts: FRIENDZONE, 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: -59m59s, deviation: 1h43m53s, median: 0s
| smb2-time: 
|   date: 2023-06-10T20:48:00
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: FRIENDZONE, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: friendzone
|   NetBIOS computer name: FRIENDZONE\x00
|   Domain name: \x00
|   FQDN: friendzone
|_  System time: 2023-06-10T23:47:56+03:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jun 10 21:48:08 2023 -- 1 IP address (1 host up) scanned in 42.05 seconds
```

## Enumerating port 53 (domain)

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/FriendZone]
└─$ dig axfr friendzone.red @10.10.10.123

; <<>> DiG 9.18.12-1-Debian <<>> axfr friendzone.red @10.10.10.123
;; global options: +cmd
friendzone.red.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
friendzone.red.         604800  IN      AAAA    ::1
friendzone.red.         604800  IN      NS      localhost.
friendzone.red.         604800  IN      A       127.0.0.1
administrator1.friendzone.red. 604800 IN A      127.0.0.1
hr.friendzone.red.      604800  IN      A       127.0.0.1
uploads.friendzone.red. 604800  IN      A       127.0.0.1
friendzone.red.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
;; Query time: 256 msec
;; SERVER: 10.10.10.123#53(10.10.10.123) (TCP)
;; WHEN: Sat Jun 10 23:11:13 WAT 2023
;; XFR size: 8 records (messages 1, bytes 289)
```
cool we got 2 subdomains

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/FriendZone]
└─$ cat /etc/hosts | grep "friendzone.red"
10.10.10.123    friendzone.red uploads.friendzone.red administrator1.friendzone.red
```
Add them to `/etc/hosts` file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2f1ee9bc-2ac9-40df-ba18-2cb0007942d9)

opening `https://administrator1.friendzone.red/` on a browser, we got directed to a login page. let's check around for creds. 


## Enumerating SMB

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/FriendZone]
└─$ smbclient -L friendzone.red                                                                                                                                        
Password for [WORKGROUP\n16hth4wk]:
                                                                                   
        Sharename       Type      Comment 
        ---------       ----      -------                 
        print$          Disk      Printer Drivers
        Files           Disk      FriendZone Samba Server Files /etc/Files
        general         Disk      FriendZone Samba Server Files
        Development     Disk      FriendZone Samba Server Files
        IPC$            IPC       IPC Service (FriendZone server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.                             
                                                                                   
        Server               Comment
        ---------            -------                                               
                                         
        Workgroup            Master                                                                                                                                    
        ---------            -------
        WORKGROUP            FRIENDZONE
                                                                                   
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/FriendZone]
└─$ smbclient //friendzone.red/Files                                                
Password for [WORKGROUP\n16hth4wk]:                                                
tree connect failed: NT_STATUS_ACCESS_DENIED
                                                                                   
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/FriendZone]                
└─$ smbclient //friendzone.red/general
Password for [WORKGROUP\n16hth4wk]:                                                
Try "help" to get a list of possible commands.
smb: \> ls    
  .                                   D        0  Wed Jan 16 21:10:51 2019
  ..                                  D        0  Tue Sep 13 15:56:24 2022
  creds.txt                           N       57  Wed Oct 10 00:52:42 2018

                3545824 blocks of size 1024. 1651408 blocks available
smb: \> get creds.txt 
getting file \creds.txt of size 57 as creds.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \> 
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/FriendZone]
└─$ smbclient //friendzone.red/Development
Password for [WORKGROUP\n16hth4wk]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun Jun 11 00:12:29 2023
  ..                                  D        0  Tue Sep 13 15:56:24 2022

                3545824 blocks of size 1024. 1651408 blocks available
smb: \> put
put <filename>
smb: \> put normal.txt 
putting file normal.txt as \normal.txt (4.7 kb/s) (average 4.7 kb/s)
smb: \> 
```
enumerating smb, we got 3 shares availble. `Files` share was not accessable, `general` share got a cred, and `Development` share we have file upload permission.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/FriendZone]
└─$ cat creds.txt 
creds for the admin THING:

admin:WORKWORKHhallelujah@#
```
checking the content of `cred.txt` we can see it is an admin credential.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b76d89d1-2345-4237-858b-fce7a0620f45)

using this cred to login `https://administrator1.friendzone.red/login.php` we are ask to navigate to `/dashboard.php`

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c342e8aa-d38d-4332-82b3-917f176ae2e4)

navigating to `/dashboard.php` 


