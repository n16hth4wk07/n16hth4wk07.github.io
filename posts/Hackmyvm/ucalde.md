first start enumeration with nmap

```
# Nmap 7.93 scan initiated Sun Feb 26 11:03:59 2023 as: nmap -sC -sV -T4 -oN normal.tcp -p 21,22,80 -Pn 192.168.0.102
Nmap scan report for 192.168.0.102
Host is up (0.00055s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1000     1000         5154 Jan 28 19:54 output
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.0.101
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 3a09a4dad7db99eea55105e9afe70890 (RSA)
|   256 cb426abe22132cf257f980d1f7fb885c (ECDSA)
|_  256 443cb40faac394fa231519e3e5185694 (ED25519)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
|_http-server-header: Apache/2.4.54 (Debian)
|_http-title: Agency - Start Bootstrap Theme
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Feb 26 11:04:12 2023 -- 1 IP address (1 host up) scanned in 13.46 seconds
```
cool we know what ports are open now, let's start enumeration with port 21 first.

## Enumerating port 21 (FTP)

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ ftp 192.168.0.102
Connected to 192.168.0.102.
220 (vsFTPd 3.0.3)
Name (192.168.0.102:n16hth4wk): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||42148|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000         5154 Jan 28 19:54 output
226 Directory send OK.
ftp> get output
local: output remote: output
229 Entering Extended Passive Mode (|||9031|)
150 Opening BINARY mode data connection for output (5154 bytes).
100% |*************************************************************************************************************************|  5154       16.06 MiB/s    00:00 ETA
226 Transfer complete.
5154 bytes received in 00:00 (5.70 MiB/s)
ftp> ^D
221 Goodbye.
```
from our nmap scan, we can see anonymous login is allowed for ftp. logged in and we got a file `output`, downloaded the file into our machine. also i tried to upload a file, got permission denied. let's check the content of `output`

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ cat output                                                                     
Script démarré sur 2023-01-28 19:54:05+01:00 [TERM="xterm-256color" TTY="/dev/pts/0" COLUMNS="105" LINES="25"]
matthew@debian:~$ id                                                               
uid=1000(matthew) gid=1000(matthew) groupes=1000(matthew)    
matthew@debian:~$ ls -al                                                           
total 32                                                                           
drwxr-xr-x 4 matthew matthew 4096 28 janv. 19:54 .     
drwxr-xr-x 3 root    root    4096 23 janv. 07:52 ..      
lrwxrwxrwx 1 root    root       9 23 janv. 07:53 .bash_history -> /dev/null
-rw-r--r-- 1 matthew matthew  220 23 janv. 07:51 .bash_logout
-rw-r--r-- 1 matthew matthew 3526 23 janv. 07:51 .bashrc                                                                                                              
drwx------ 3 matthew matthew 4096 23 janv. 08:04 .config                         
drwxr-xr-x 3 matthew matthew 4096 23 janv. 08:04 .local                          
-rw-r--r-- 1 matthew matthew  807 23 janv. 07:51 .profile                        
-rw-r--r-- 1 matthew matthew    0 28 janv. 19:54 typescript                     
-rwx------ 1 matthew matthew   33 23 janv. 07:53 user.txt                       
matthew@debian:~$ toilet -f mono12 -F metal hackmyvm.eu                             
                                                                                
 ▄▄                            ▄▄                                               
 ██                            ██                                               
 ██▄████▄   ▄█████▄   ▄█████▄  ██ ▄██▀   ████▄██▄  ▀██  ███  ██▄  ▄██  ████▄██▄  
 ██▀   ██   ▀ ▄▄▄██  ██▀    ▀  ██▄██     ██ ██ ██   ██▄ ██    ██  ██   ██ ██ ██  
 ██    ██  ▄██▀▀▀██  ██        ██▀██▄    ██ ██ ██    ████▀    ▀█▄▄█▀   ██ ██ ██  
 ██    ██  ██▄▄▄███  ▀██▄▄▄▄█  ██  ▀█▄   ██ ██ ██     ███      ████    ██ ██ ██  
 ▀▀    ▀▀   ▀▀▀▀ ▀▀    ▀▀▀▀▀   ▀▀   ▀▀▀  ▀▀ ▀▀ ▀▀     ██        ▀▀     ▀▀ ▀▀ ▀▀  
                                                    ███                          
                                                                                 
                                                                                 
                                                                                 
                                                                                 
            ▄████▄   ██    ██                                                    
           ██▄▄▄▄██  ██    ██                                                    
           ██▀▀▀▀▀▀  ██    ██                                                    
    ██     ▀██▄▄▄▄█  ██▄▄▄███                                                    
    ▀▀       ▀▀▀▀▀    ▀▀▀▀ ▀▀                                                   
                                                                                 
                                                                                
matthew@debian:~$ exit
exit                                                                               
                                                                                   
Script terminé sur 2023-01-28 19:54:37+01:00 [COMMAND_EXIT_CODE="0"]
```
checking the content of `output` we got an output of an ssh user dir. and we can see the username `matthew`. let's keep this name in our note. moving to the next port `80`.


## Enumerating Port 80 (http)

![image](https://user-images.githubusercontent.com/87468669/221404871-48a2e425-f667-4eef-a3a8-8cd136bb76ee.png)

opening the IP on our browser, we got a web page service all about designs and the likes, navigating around, checked source page got nothing . let's burst hidden dirs.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ ffuf -u "http://192.168.0.102/FUZZ" -w /usr/share/wordlists/dirb/big.txt  -fc 403 -fs 0 -ic  -e .txt,.php,.bak,.sql,.zip 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.0.102/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/big.txt
 :: Extensions       : .txt .php .bak .sql .zip 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
 :: Filter           : Response size: 0
________________________________________________

create_account.php      [Status: 200, Size: 1003, Words: 238, Lines: 48, Duration: 16ms]
css                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 8ms]
img                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 26ms]
index.php               [Status: 200, Size: 29604, Words: 8689, Lines: 715, Duration: 9ms]
js                      [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 12ms]
login.php               [Status: 200, Size: 1022, Words: 126, Lines: 61, Duration: 14ms]
mail                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 9ms]
vendor                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 8ms]
:: Progress: [122814/122814] :: Job [1/1] :: 3436 req/sec :: Duration: [0:00:29] :: Errors: 0 ::
```
cool we got hidden dirs, let's create an account. 

![image](https://user-images.githubusercontent.com/87468669/221405294-2eb6e353-d3b9-4956-9053-ebb52539aee3.png)



after creating an account, got a base64 encoded text in the url link. copied it 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Hackmyvm/Uvalde]
└─$ echo "dXNlcm5hbWU9bmlnaHRoYXdrJnBhc3N3b3JkPW5pZ2h0aGF3azIwMjNANjA4MQ==" | base64 -d 
username=nighthawk&password=nighthawk2023@6081 
```
decoded the text and we got to see creates the accout success but it automatically gives us a password. let's login 




