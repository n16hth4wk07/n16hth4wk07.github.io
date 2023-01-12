First enumeration with nmap 

```
nmap -sC -sV -oN normal.tcp -p 21,80 -Pn <IP>
```

```
# Nmap 7.93 scan initiated Wed Jan 11 21:55:44 2023 as: nmap -sC -sV -oN normal.tcp -p 21,80 -Pn 192.168.143.183
Nmap scan report for 192.168.143.183
Host is up (0.22s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
80/tcp open  http    Apache httpd 2.4.41
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: 403 Forbidden
Service Info: Host: 127.0.0.1; OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jan 11 21:55:59 2023 -- 1 IP address (1 host up) scanned in 15.08 seconds
```

now we know what services are running on those ports, let's start with port 80.

![image](https://user-images.githubusercontent.com/87468669/211977517-e262597e-1f36-4aad-9210-80f0332714d6.png)

opening the IP on a browser, got 403 forbidden responds, tried many bypass strings and tricks, did not work.

![image](https://user-images.githubusercontent.com/87468669/211977567-7da96559-c10c-4272-9234-8a6f139f0217.png)

tried intercepting requests and modifying the headers, still did not work. let's move to port 21. searching online for common ftp creds i saw [this](https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt). therefore cred `user:system` worked.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Exghost]
└─$ ftp 192.168.100.183 
Connected to 192.168.100.183.
220 (vsFTPd 3.0.3)
Name (192.168.100.183:n16hth4wk): user
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -al
229 Entering Extended Passive Mode (|||46486|)
ftp: Can't connect to `192.168.100.183:46486': Connection timed out
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        118          4096 Feb 25  2022 .
drwxr-xr-x    2 0        118          4096 Feb 25  2022 ..
-rwxrwxrwx    1 0        0          126151 Jan 27  2022 backup
226 Directory send OK.
ftp> get backup
local: backup remote: backup
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for backup (126151 bytes).
100% |*************************************************************************************************************************|   123 KiB   66.29 KiB/s    00:00 ETA
226 Transfer complete.
126151 bytes received in 00:02 (59.24 KiB/s)
ftp> put normal.tcp 
local: normal.tcp remote: normal.tcp
200 EPRT command successful. Consider using EPSV.
550 Permission denied.
ftp> 
```

logged in, saw a backup file, downloaded the backup file. now let's check the content of the backup file. also i tried file upload, it failed permission denied.

![image](https://user-images.githubusercontent.com/87468669/211977635-8ca6a7a4-061e-40d1-b2f2-b59767496f30.png)

the backup file is a pcap captured file, let's use wireshark to check the content of the file.

![image](https://user-images.githubusercontent.com/87468669/211977708-25964d2a-eca4-4f38-baca-ddac767d8ef9.png)

while going through the backup file with wireshark, i saw an http stream, following the stream got us this http request and source code. so i copied the html source code to create an upload form page.

```
<html lang="en">

<head>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
</head>
<body>
	<!-- Part from this tutorial -->
	<form method="post" action="http://192.168.100.183/exiftest.php" enctype="multipart/form-data">
		<input type="file" name="myFile" />
		<input type="submit" value="Upload">
	</form>
	<!-- End of part from this tutorial -->
</body>
</html>
```

![image](https://user-images.githubusercontent.com/87468669/211977768-8859d4f1-4ba5-4bf6-b6b9-9ce84ae0d0a1.png)

in the response page we can see it only accepts jpeg images. now let's open the html file using our browser.

![image](https://user-images.githubusercontent.com/87468669/211977805-b25554da-0be5-4a38-a994-52e54d38145b.png)

uploaded an image file success... let's get shell.

![image](https://user-images.githubusercontent.com/87468669/211977831-c8c9c2b0-7a5c-4c31-a8b1-52b3ca77cac0.png)

as we can see above the exiftool version number is `12.23`. let's search for exploit on it.

![image](https://user-images.githubusercontent.com/87468669/211977901-6673a4d5-35ab-474d-b160-ce765509d460.png)

after so much researching, i found a working exploit from github [exploit](https://github.com/AssassinUKG/CVE-2021-22204). now let's run the exploit.

![image](https://user-images.githubusercontent.com/87468669/211978017-86240377-5e0b-42e0-9f1e-fa14432cff93.png)

ran the exploit and it modified the image, inject an arbitary RCE into the image file. fire up ncat listener and let's upload the image.

![image](https://user-images.githubusercontent.com/87468669/211978060-785cf724-51ad-4b24-9554-9aca16b9be53.png)

uploaded the image and check back on our ncat listener.

![image](https://user-images.githubusercontent.com/87468669/211978091-11e858b7-dfcf-46e2-84d9-228d7508e600.png)

Boom!!! we got a reverse shell.

## Privilege Escalation 

![image](https://user-images.githubusercontent.com/87468669/211989703-5a828a33-c988-4b02-8301-201d9eccbb36.png)

running linpeas, i found out the sudo version is vuln to `CVE-2021-4034` which is `pwnkit` 

![image](https://user-images.githubusercontent.com/87468669/211989063-3336703c-fc5c-42db-9e83-8b86604135bf.png)

searched for an exploit and found a github exploit. [cve-2021-4034](https://github.com/joeammond/CVE-2021-4034). 

![image](https://user-images.githubusercontent.com/87468669/211988813-ecb445c6-22bb-411b-873f-d20616ae1dbc.png)

Downloaded and ran the exploit and it dropped up to a root shell.🤠

