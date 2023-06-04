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
/index.php            (Status: 200) [Size: 6690]
/help.php             (Status: 200) [Size: 6689]
/themes               (Status: 301) [Size: 0] [--> https://10.10.10.60/themes/]
/stats.php            (Status: 200) [Size: 6690]
/css                  (Status: 301) [Size: 0] [--> https://10.10.10.60/css/]
/edit.php             (Status: 200) [Size: 6689]
/includes             (Status: 301) [Size: 0] [--> https://10.10.10.60/includes/]
/license.php          (Status: 200) [Size: 6692]
/system.php           (Status: 200) [Size: 6691]
/status.php           (Status: 200) [Size: 6691]
/javascript           (Status: 301) [Size: 0] [--> https://10.10.10.60/javascript/]
/changelog.txt        (Status: 200) [Size: 271]
/classes              (Status: 301) [Size: 0] [--> https://10.10.10.60/classes/]
/exec.php             (Status: 200) [Size: 6689]
/widgets              (Status: 301) [Size: 0] [--> https://10.10.10.60/widgets/]
/graph.php            (Status: 200) [Size: 6690]
/tree                 (Status: 301) [Size: 0] [--> https://10.10.10.60/tree/]
/wizard.php           (Status: 200) [Size: 6691]
/shortcuts            (Status: 301) [Size: 0] [--> https://10.10.10.60/shortcuts/]
/pkg.php              (Status: 200) [Size: 6688]
/installer            (Status: 301) [Size: 0] [--> https://10.10.10.60/installer/]
/wizards              (Status: 301) [Size: 0] [--> https://10.10.10.60/wizards/]
/xmlrpc.php           (Status: 200) [Size: 384]
/reboot.php           (Status: 200) [Size: 6691]
/interfaces.php       (Status: 200) [Size: 6695]
/csrf                 (Status: 301) [Size: 0] [--> https://10.10.10.60/csrf/]
/system-users.txt     (Status: 200) [Size: 106]
/filebrowser          (Status: 301) [Size: 0] [--> https://10.10.10.60/filebrowser/]
/%7Echeckout%7E       (Status: 403) [Size: 345]tus: 200) [Size: 384]  

===============================================================
2023/06/04 01:50:11 Finished
===============================================================
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e16158c9-bd0e-46bd-ba18-ac7d779ed76f)

navigating to `/changlog.txt` dir, we can see they patched 2/3 vuln of this firewall.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/8773335c-d279-4973-b2c7-71572aa79509)

checking the `/system-users.txt`, we can see a username . let's try login using this username and default password `pfsense`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/30109f59-d55e-4b12-b341-7b72b6e8a9af)

logged in with cred `rohit:pfsense`. let's play around and see what we can get.








