![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f1653eb0-8367-487c-a54e-b6b925e54945)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Mon Jun  5 00:36:52 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,139,445,1880,9999 -Pn 10.10.10.111
Nmap scan report for 10.10.10.111                                                  
Host is up (0.14s latency).
                                                                                   
PORT     STATE SERVICE     VERSION                                                 
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:                                                                     
|   2048 877b912a0f11b6571ecb9f77cf35e221 (RSA)
|   256 b79b06ddc25e284478411e677d1eb762 (ECDSA)
|_  256 21cf166d82a430c3c69cd738bab502b0 (ED25519)            
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)                                                                                                 
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
1880/tcp open  http        Node.js (Express middleware)
|_http-title: Node-RED        
9999/tcp open  http        nginx 1.10.3 (Ubuntu)
|_http-server-header: nginx/1.10.3 (Ubuntu)           
|_http-title: Welcome to nginx!
Service Info: Host: FROLIC; OS: Linux; CPE: cpe:/o:linux:linux_kernel
                                                                                   
Host script results:
|_clock-skew: mean: -1h49m59s, deviation: 3h10m30s, median: 0s
|_nbstat: NetBIOS name: FROLIC, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-security-mode: 
|   account_used: guest                                                            
|   authentication_level: user
|   challenge_response: supported    
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   311:                                                                           
|_    Message signing enabled but not required
| smb2-time:                                                                                                                                                           
|   date: 2023-06-04T23:37:07                                                                                                                                          
|_  start_date: N/A
| smb-os-discovery:                                                                
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: frolic     
|   NetBIOS computer name: FROLIC\x00
|   Domain name: \x00                                                              
|   FQDN: frolic      
|_  System time: 2023-06-05T05:07:07+05:30                                         
                                                                                   
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                         
# Nmap done at Mon Jun  5 00:37:12 2023 -- 1 IP address (1 host up) scanned in 19.78 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2f1799ea-8c34-46cf-9902-bb614a748b9e)

noticed a web server running on port `1880`, opening it on browser, got redirected to a login page. as we can see it is running `node-red` service. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/059f08cc-ac30-41aa-b359-9aec415386a0)

noticed another web server runing on port `9999`, opening it on a browser, got a default nginx web page. let's fuzz for hidden dirs.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Frolic]         
└─$ gobuster dir -u http://forlic.htb:9999/ -w /usr/share/wordlists/dirb/common.txt -k -x php,txt -b 403,404 2>/dev/null 
===============================================================
Gobuster v3.5                                                                                                                                                          
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)                      
===============================================================                                                                                                        
[+] Url:                     http://forlic.htb:9999/
[+] Method:                  GET                                                   
[+] Threads:                 10     
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt                  
[+] Negative Status codes:   403,404                                               
[+] User Agent:              gobuster/3.5                          
[+] Extensions:              php,txt                                                                                                                                   
[+] Timeout:                 10s                                                   
===============================================================                                                                                                        
2023/06/05 00:50:25 Starting gobuster in directory enumeration mode              
===============================================================                   
/admin                (Status: 301) [Size: 194] [--> http://forlic.htb:9999/admin/]
/backup               (Status: 301) [Size: 194] [--> http://forlic.htb:9999/backup/]
/dev                  (Status: 301) [Size: 194] [--> http://forlic.htb:9999/dev/]
/test                 (Status: 301) [Size: 194] [--> http://forlic.htb:9999/test/]
                                         
===============================================================
2023/06/05 00:53:48 Finished                                                                                                                                           
===============================================================
```
cool, let's check em one by one.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/53c33584-311f-42a1-98fd-5fa1297bf3f0)

navigating to `/admin` dir, got to a login page 

