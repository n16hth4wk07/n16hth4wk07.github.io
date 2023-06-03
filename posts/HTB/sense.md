![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c02d04a0-b461-42af-b136-4104a84343ac)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Sat Jun  3 17:47:04 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 80,443 -Pn 10.10.10.60
Nmap scan report for 10.10.10.60
Host is up (0.16s latency).

PORT    STATE SERVICE  VERSION
80/tcp  open  http     lighttpd 1.4.35
|_http-server-header: lighttpd/1.4.35
|_http-title: Did not follow redirect to https://10.10.10.60/
443/tcp open  ssl/http lighttpd 1.4.35
|_http-server-header: lighttpd/1.4.35
|_ssl-date: TLS randomness does not represent time
|_http-title: Login
| ssl-cert: Subject: commonName=Common Name (eg, YOUR name)/organizationName=CompanyName/stateOrProvinceName=Somewhere/countryName=US
| Not valid before: 2017-10-14T19:21:35
|_Not valid after:  2023-04-06T19:21:35

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jun  3 17:47:31 2023 -- 1 IP address (1 host up) scanned in 26.82 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2b538c44-d72a-4aa1-8b85-cda1239a0c20)

opening the IP on browser, got redirected to a login page, and we can see it is running `pfsense` service. tried default creds no sucess. let's fuzz

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Sense]
└─$ gobuster dir -u https://10.10.10.60/ -w /usr/share/wordlists/dirb/common.txt -x php,txt,bak,sql 2>/dev/null -k 
===============================================================                
Gobuster v3.5                                                                      
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)                   
===============================================================
[+] Url:                     https://10.10.10.60/
[+] Method:                  GET                                                   
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5                      
[+] Extensions:              sql,php,txt,bak
[+] Timeout:                 10s                                                   
===============================================================
2023/06/03 17:58:14 Starting gobuster in directory enumeration mode                
===============================================================
/changelog.txt        (Status: 200) [Size: 271]                
/classes              (Status: 301) [Size: 0] [--> https://10.10.10.60/classes/]
/css                  (Status: 301) [Size: 0] [--> https://10.10.10.60/css/]       
/edit.php             (Status: 200) [Size: 6689]                                   
/exec.php             (Status: 200) [Size: 6689]                 
/favicon.ico          (Status: 200) [Size: 1406]                                   
/graph.php            (Status: 200) [Size: 6690]
/help.php             (Status: 200) [Size: 6689]
/includes             (Status: 301) [Size: 0] [--> https://10.10.10.60/includes/]  
/index.php            (Status: 200) [Size: 6690]               
/index.html           (Status: 200) [Size: 329]                    
/index.php            (Status: 200) [Size: 6690]               
/installer            (Status: 301) [Size: 0] [--> https://10.10.10.60/installer/] 
/javascript           (Status: 301) [Size: 0] [--> https://10.10.10.60/javascript/]
/license.php          (Status: 200) [Size: 6692]     
/pkg.php              (Status: 200) [Size: 6688]                                                                                                                       
/stats.php            (Status: 200) [Size: 6690]                                   
/status.php           (Status: 200) [Size: 6691]     
/system.php           (Status: 200) [Size: 6691]                                                                                                                       
/themes               (Status: 301) [Size: 0] [--> https://10.10.10.60/themes/]
/tree                 (Status: 301) [Size: 0] [--> https://10.10.10.60/tree/]      
/widgets              (Status: 301) [Size: 0] [--> https://10.10.10.60/widgets/]
/wizard.php           (Status: 200) [Size: 6691]               
/xmlrpc.php           (Status: 200) [Size: 384]  
                                         
===============================================================  
2023/06/03 18:05:44 Finished    
===============================================================
```


