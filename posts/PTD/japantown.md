![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f6767867-a838-4b83-ae53-f44b4804638b)

first enumeration with nmap 

```
# Nmap 7.93 scan initiated Sun Jun 11 19:27:42 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 22,80 -Pn 10.150.150.21
Nmap scan report for 10.150.150.21
Host is up (0.19s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 8e76475be45eacc4f3377fab038d1520 (RSA)
|   256 f9d401a071ee66aed6dcc4392dab04c4 (ECDSA)
|_  256 f44c5d62f1d104f67feabbee82d99b6f (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_*/2020/12/you-know-what-this-is-for.png
|_http-title: Japantown &#8211; &#8211; experience &amp; live Asia
|_http-generator: WordPress 5.3.6
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jun 11 19:28:00 2023 -- 1 IP address (1 host up) scanned in 18.29 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0f98fc73-7321-4d59-bf54-676a424b875c)

opening the IP on a browser, we got directed to a wordpress blog, let's play around 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4a8ed290-6c71-4265-8579-d8214826acb6)

playing around the web, found a blog and in the blog the author `kim` is talking about a depixle tool and there's also a pixlized image with a bluured password. let's download the image into our box. now let's fuzz.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.21]
└─$ gobuster dir -u http://10.150.150.21/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -k -b 403,404 -x php,txt 2>/dev/null
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.150.150.21/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   403,404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php,txt
[+] Timeout:                 10s
===============================================================
2023/06/11 19:52:28 Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 301) [Size: 0] [--> http://10.150.150.21/]
/blog                 (Status: 301) [Size: 313] [--> http://10.150.150.21/blog/]
/wp-content           (Status: 301) [Size: 319] [--> http://10.150.150.21/wp-content/]
/wp-login.php         (Status: 200) [Size: 4986]
/wp-includes          (Status: 301) [Size: 320] [--> http://10.150.150.21/wp-includes/]
/javascript           (Status: 301) [Size: 319] [--> http://10.150.150.21/javascript/]
/robots.txt           (Status: 200) [Size: 111]
/wp-trackback.php     (Status: 200) [Size: 135]
/wp-admin             (Status: 301) [Size: 317] [--> http://10.150.150.21/wp-admin/]
/xmlrpc.php           (Status: 405) [Size: 42]
/wp-signup.php        (Status: 302) [Size: 0] [--> http://10.150.150.21/blog/wp-login.php?action=register]
```
cool we have robots.txt available, let's check what's there.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d0bc868a-c784-45ae-8758-8b76d1a7a89c)

found an image file location in robots.txt `*/2020/12/you-know-what-this-is-for.png`, let's look for this image file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/681db2c8-3f83-472e-beb0-c3b21206dd67)

after several minutes of brainstorming the location of this image file, found it in `/wp-content/uploads/`dir, as we can see the image file is not displaying. let's download it into our box and try repair or know what type of file it is.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.21]
└─$ wget http://10.150.150.21/wp-content/uploads/2020/12/you-know-what-this-is-for.png
--2023-06-11 21:09:58--  http://10.150.150.21/wp-content/uploads/2020/12/you-know-what-this-is-for.png
Connecting to 10.150.150.21:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 174584 (170K) [image/png]
Saving to: ‘you-know-what-this-is-for.png’

you-know-what-this-is-for.png             100%[====================================================================================>] 170.49K  83.2KB/s    in 2.0s    

2023-06-11 21:10:00 (83.2 KB/s) - ‘you-know-what-this-is-for.png’ saved [174584/174584]

                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.21]
└─$ file you-know-what-this-is-for.png                                                                                                                   
you-know-what-this-is-for.png: ASCII text
```
downloaded the file and checking the file type we can see it is an ascii text file. let's read the content of the file.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/929a6664-4da6-47ed-9226-60db5abf9723)

it is a base64 encoding, let's decode and output it to a file.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.21]
└─$ cat you-know-what-this-is-for.png | base64 -d > image
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/10.150.150.21]
└─$ file image 
image: PNG image data, 1499 x 607, 8-bit/color RGB, non-interlaced
```
decoded from base64 and piped the output to a file, checking the file type we can see it is a png file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d47bd8ca-3ea6-4b29-b743-a5b30e97c817)

opening the image file, we can see it has some weird texts, let's research out depiexlizer image.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d4900023-9474-4d6d-ad9e-04e0b84e637f)

found a tool on github, check out [here](https://github.com/beurtschipper/Depix). read about this tooll and understand it. noticed that the image file we got earlier is a wordlist to bruteforce the blurred image. let's try it out.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3422ae06-ca7b-4c83-b0af-39a501c3dfdb)

let's crop this image.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/bf98d2c6-60c9-44d1-97de-53d24213659f)

crop out the blurred part only


