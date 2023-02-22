![image](https://user-images.githubusercontent.com/87468669/220750361-2f97cf1d-7e7d-4853-b097-787594ec3faa.png)

first start enumeration with nmap.

```
# Nmap 7.93 scan initiated Wed Feb 22 21:26:21 2023 as: nmap -sC -sV -T4 -oN normal.tcp -p 21,80,139,445,1055 -Pn 10.150.150.55
Nmap scan report for 10.150.150.55
Host is up (0.19s latency).            
                                         
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd
| ftp-syst:                             
|   STAT:                              
| FTP server status:                                                               
|      Connected to ::ffff:10.66.67.146                                            
|      Logged in as ftp                                                            
|      TYPE: ASCII
|      No session bandwidth limit                                                  
|      Session timeout in seconds is 300                                           
|      Control connection is plain text                                            
|      Data connections will be plain text  
|      At session startup, client count was 2      
|      vsFTPd 3.0.3 - secure, fast, stable 
|_End of status                                                                    
| ftp-anon: Anonymous FTP login allowed (FTP code 230)                        
|_-rw-r--r--    1 0        0              13 Jun 12  2020 test
80/tcp   open  http        Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
139/tcp  open  netbios-ssn Samba smbd 4.6.2                                                                                                                           
445/tcp  open  netbios-ssn Samba smbd 4.6.2
1055/tcp open  ssh         OpenSSH 8.2p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
                                         
Host script results:
|_clock-skew: 39m10s                                                               
|_nbstat: NetBIOS name: UBUNTU, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb2-time:                                                                                                                                                          
|   date: 2023-02-22T21:05:47                                                                                                                                         
|_  start_date: N/A
| smb2-security-mode:                                                              
|   311: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Feb 22 21:26:41 2023 -- 1 IP address (1 host up) scanned in 20.29 seconds
```

![image](https://user-images.githubusercontent.com/87468669/220751664-8713f0b1-e433-4bd2-ac49-0476d0c671ba.png)

noticed a webserver running on port 80, opening it on a browser, we got directed to an apache default webpage. checking the page source we got nothing, let's burst hidden dirs

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/Silence/private]
└─$ dirsearch -u http://10.150.150.55/ -w /usr/share/wordlists/dirb/common.txt

  _|. _ _  _  _  _ _|_    v0.4.2
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 4613

Output File: /home/n16hth4wk/.dirsearch/reports/10.150.150.55/-_23-02-22_21-41-53.txt

Error Log: /home/n16hth4wk/.dirsearch/logs/errors-23-02-22_21-41-53.log

Target: http://10.150.150.55/

[21:41:54] Starting: 
[21:42:27] 200 -    2KB - /index.php                                        
[21:42:27] 200 -   11KB - /index.html                                       
[21:42:37] 200 -   69KB - /info.php                                         
[21:43:10] 403 -  278B  - /server-status
[21:43:10] 403 -  278B  - /server-status                                    
                                                                             
Task Completed
```
cool let's check out 👀 the `/index.php` dir. 

![image](https://user-images.githubusercontent.com/87468669/220754927-bb313aa8-2486-4d9c-b762-b4e63fd9cc3f.png)

cheking the `index.php` dir, we got directed to a file browser and looking at the page we can see a potential hint `Path is not a directory`. 🤔 what can we do with that?. 

![image](https://user-images.githubusercontent.com/87468669/220756552-2dbf3871-6745-49a5-b8e3-75613357f506.png)

after playing around for some time, added `?path=/` to the `index.php` parameter. and boom we got something sweet 😅. let's play around more to see if we can get anything.

![image](https://user-images.githubusercontent.com/87468669/220761090-b751dead-39e7-485e-9c06-e45910f91798.png)

playing around, cheching dirs and file in the home dir, we got to see some files that looks like web files in `/home/sally/.ssh`... hmmmm 🤔 what are they doing in .ssh dir?

![image](https://user-images.githubusercontent.com/87468669/220761679-1fec7491-3149-471f-80e4-30640554af18.png)

also noticed a `SSHArchiveBackup.tar.gz` file in `/home/sally/backup/` dir. trying to access them directly was not working, let's try check those filels we saw earlier in the .ssh dir.

![image](https://user-images.githubusercontent.com/87468669/220762536-265546a8-574e-450c-b4c9-099b92f33717.png)

testing the files one by one, the `trick.php` looked sus, looks like a potential lfi path. let's fuzz for parameter 








