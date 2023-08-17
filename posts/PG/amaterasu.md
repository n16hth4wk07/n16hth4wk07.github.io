First enumeration with nmap 

first ran a full port scan `nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn $IP`
```
# Nmap 7.94 scan initiated Wed Aug 16 21:28:50 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.172.249
Warning: 192.168.172.249 giving up on port because retransmission cap hit (10).
Nmap scan report for 192.168.172.249
Host is up (0.22s latency).
Not shown: 65427 filtered tcp ports (no-response), 104 closed tcp ports (conn-refused)
PORT      STATE SERVICE
21/tcp    open  ftp
25022/tcp open  unknown
33414/tcp open  unknown
40080/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Wed Aug 16 21:40:52 2023 -- 1 IP address (1 host up) scanned in 722.37 seconds
```
cool let's run nmap default script on the open ports 


```
# Nmap 7.94 scan initiated Thu Aug 17 07:59:08 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 21,25022,33414,40080 -Pn 192.168.172.249
Nmap scan report for 192.168.172.249
Host is up (0.28s latency).

PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.45.161
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
25022/tcp open  ssh     OpenSSH 8.6 (protocol 2.0)
| ssh-hostkey: 
|   256 68:c6:05:e8:dc:f2:9a:2a:78:9b:ee:a1:ae:f6:38:1a (ECDSA)
|_  256 e9:89:cc:c2:17:14:f3:bc:62:21:06:4a:5e:71:80:ce (ED25519)
33414/tcp open  unknown
| fingerprint-strings: 
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 404 NOT FOUND
|     Server: Werkzeug/2.2.3 Python/3.9.13
|     Date: Thu, 17 Aug 2023 06:59:17 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 207
|     Connection: close
|     <!doctype html>
|     <html lang=en>
|     <title>404 Not Found</title>
|     <h1>Not Found</h1>
|     <p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>
|   Help: 
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
|     "http://www.w3.org/TR/html4/strict.dtd">
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request syntax ('HELP').</p>
|     <p>Error code explanation: HTTPStatus.BAD_REQUEST - Bad request syntax or unsupported method.</p>
|     </body>
|     </html>
|   RTSPRequest: 
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
|     "http://www.w3.org/TR/html4/strict.dtd">
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request version ('RTSP/1.0').</p>
|     <p>Error code explanation: HTTPStatus.BAD_REQUEST - Bad request syntax or unsupported method.</p>
|     </body>
|_    </html>
40080/tcp open  http    Apache httpd 2.4.53 ((Fedora))
|_http-server-header: Apache/2.4.53 (Fedora)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: My test page
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port33414-TCP:V=7.94%I=7%D=8/17%Time=64DDC545%P=x86_64-pc-linux-gnu%r(G
SF:etRequest,184,"HTTP/1\.1\x20404\x20NOT\x20FOUND\r\nServer:\x20Werkzeug/
SF:2\.2\.3\x20Python/3\.9\.13\r\nDate:\x20Thu,\x2017\x20Aug\x202023\x2006:
SF:59:17\x20GMT\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent
SF:-Length:\x20207\r\nConnection:\x20close\r\n\r\n<!doctype\x20html>\n<htm
SF:l\x20lang=en>\n<title>404\x20Not\x20Found</title>\n<h1>Not\x20Found</h1
SF:</head>\n\x20\x20\x20\x20<body>\n\x20\x20\x20\x20\x20\x20\x20\x20<h1>Er
SF:ror\x20response</h1>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\x20code:
SF:\x20400</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Message:\x20Bad\x20requ
SF:est\x20syntax\x20\('HELP'\)\.</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>E
SF:rror\x20code\x20explanation:\x20HTTPStatus\.BAD_REQUEST\x20-\x20Bad\x20
SF:request\x20syntax\x20or\x20unsupported\x20method\.</p>\n\x20\x20\x20\x2
SF:0</body>\n</html>\n");
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Aug 17 08:01:21 2023 -- 1 IP address (1 host up) scanned in 133.47 seconds
```
cool we got more info about the ports, let's enuerate further.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7e6ae0bb-288d-46df-bd0a-7a5071702ded)

noticed a web server running on port `33414`, opening it on a browser, we got a 404 not found response on the page. let's fuzz.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PG/Amaterasu]
└─$ ffuf -ic -u "http://192.168.172.249:33414/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -fc 404,403,401  -fs 0 -e .txt,.php,.bak,.sql,.jsp

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.172.249:33414/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Extensions       : .txt .php .bak .sql .jsp 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 404,403,401
 :: Filter           : Response size: 0
________________________________________________

help                    [Status: 200, Size: 137, Words: 19, Lines: 2, Duration: 161ms]
info                    [Status: 200, Size: 98, Words: 14, Lines: 2, Duration: 172ms]
```
cool we got some dir, let's check em out

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e23f7a96-9e97-4446-b606-79e9ac9b835d)

checking out the help dir, we got to see some interesting things 
