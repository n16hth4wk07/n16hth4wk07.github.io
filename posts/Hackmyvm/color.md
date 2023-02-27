first enumeration with nmap 

```
# Nmap 7.93 scan initiated Mon Feb 27 18:15:06 2023 as: nmap -sC -sV -oN normal.tcp -p 21,80 -Pn 192.168.0.103
Nmap scan report for 192.168.0.103
Host is up (0.00089s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 1127     1127            0 Jan 27 22:45 first
| -rw-r--r--    1 1039     1039            0 Jan 27 22:45 second
| -rw-r--r--    1 0        0          290187 Feb 11 16:35 secret.jpg
|_-rw-r--r--    1 1081     1081            0 Jan 27 22:45 third
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
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
|_http-server-header: Apache/2.4.54 (Debian)
|_http-title: Document
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Feb 27 18:15:15 2023 -- 1 IP address (1 host up) scanned in 9.52 seconds
```
cool we got open ports, let's start enumerating th ports.

## Enumerating Port 21 (FTP)

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ ftp 192.168.0.103
Connected to 192.168.0.103.              
220 (vsFTPd 3.0.3)              
Name (192.168.0.103:n16hth4wk): anonymous
331 Please specify the password.
Password:                  
230 Login successful.               
Remote system type is UNIX.
Using binary mode to transfer files.                                               
ftp> ls                              
229 Entering Extended Passive Mode (|||52793|)               
150 Here comes the directory listing.                                              
-rw-r--r--    1 1127     1127            0 Jan 27 22:45 first     
-rw-r--r--    1 1039     1039            0 Jan 27 22:45 second
-rw-r--r--    1 0        0          290187 Feb 11 16:35 secret.jpg
-rw-r--r--    1 1081     1081            0 Jan 27 22:45 third
226 Directory send OK.
ftp> mget *                                                                        
mget first [anpqy?]? y                                                             
229 Entering Extended Passive Mode (|||29295|)
150 Opening BINARY mode data connection for first (0 bytes).
     0        0.00 KiB/s 
226 Transfer complete.                                                             
mget second [anpqy?]? y                                                            
229 Entering Extended Passive Mode (|||17012|)
150 Opening BINARY mode data connection for second (0 bytes).
     0        0.00 KiB/s   
226 Transfer complete.                                                             
mget secret.jpg [anpqy?]? y                                                        
229 Entering Extended Passive Mode (|||20148|)                                                                                                                        
150 Opening BINARY mode data connection for secret.jpg (290187 bytes).
100% |*************************************************************************************************************************|   283 KiB   10.34 MiB/s    00:00 ETA
226 Transfer complete.
290187 bytes received in 00:00 (10.00 MiB/s)  
mget third [anpqy?]? y                                                             
229 Entering Extended Passive Mode (|||25372|)
150 Opening BINARY mode data connection for third (0 bytes).
     0        0.00 KiB/s 
226 Transfer complete.
ftp> put normal.tcp 
local: normal.tcp remote: normal.tcp
229 Entering Extended Passive Mode (|||63750|)
550 Permission denied.
ftp> 
ftp> ^D
221 Goodbye.
```
from our nmap scan we noticed anonymous login is allowd for ftp, logged in and got some files in the ftp server downloaded them into our attack box. tried file upload but got permission denied. let's check the content of these files.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ file first 
first: empty
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ file second 
second: empty
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ file third 
third: empty
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ cat mission.txt 
Hey hacker, I've heard a lot about you and I've been told you're good. 

The FBI has hacked into my apache server and shut down my website. I need you to sneak in and retrieve the "root.txt" file. I left my credentials somewhere but I can't remember where.

I will pay you well if you succeed, good luck hacker.

┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ exiftool secret.jpg 
ExifTool Version Number         : 12.44
File Name                       : secret.jpg
Directory                       : .
File Size                       : 290 kB
File Modification Date/Time     : 2023:02:11 17:35:33+01:00
File Access Date/Time           : 2023:02:27 18:24:24+01:00
File Inode Change Date/Time     : 2023:02:27 18:24:24+01:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Image Width                     : 735
Image Height                    : 588
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 735x588
Megapixels                      : 0.432
```
checking the contents of the files, first - thrid file was empty. there was a mission asking us to hack back the server and get root.txt flag. also an image

![image](https://user-images.githubusercontent.com/87468669/221647723-a4c7011b-4abf-4ac7-8967-5d47bd199ef7.png)

cool let's try out steganography. 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ steghide extract -sf secret.jpg                     
Enter passphrase: 
steghide: could not extract any data with that passphrase!

┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ stegseek secret.jpg /usr/share/wordlists/rockyou.txt 
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "Nevermind"        

[i] Original filename: "more_secret.txt".
[i] Extracting to "secret.jpg.out".
```
tried using steghide to extract the content of the `secret.jpg` file without password, it failed so i used `stegseek` to crack the password 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ steghide extract -sf secret.jpg
Enter passphrase: 
wrote extracted data to "more_secret.txt".
                                                                                                                                                                      
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ cat more_secret.txt 
<-MnkFEo!SARTV#+D,Y4D'3_7G9D0LFWbmBCht5'AKYi.Eb-A(Bld^%E,TH.FCeu*@X0)<BOr<.BPD?sF!,R<@<<W;Dfm15Bk2*/F<G+4+EV:*DBND6+EV:.+E)./F!,aHFWb4/A0>E$/g+)2+EV:;Dg*=BAnE0-BOr;qDg-#3DImlA+B)]_C`m/1@<iu-Ec5e;FD,5.F(&Zl+D>2(@W-9>+@BRZ@q[!,BOr<.Ea`Ki+EqO;A9/l-DBO4CF`JUG@;0P!/g*T-E,9H5AM,)nEb/Zr/g*PrF(9-3ATBC1E+s3*3`'O.CG^*/BkJ\:
```
extracted the content of the `secret.jpg` file we got some weird texts. let's dcode them:

![image](https://user-images.githubusercontent.com/87468669/221651982-0bf34ad9-8e58-4f7a-bcce-d82c46efcd5a.png)

using [dcode](https://www.dcode.fr/cipher-identifier) to analyze and decode the text, we got 

```
Twenty years from now you will be more disappointed by the things that you didn't do than by the ones you did do. So throw off the bowlines. Sail away from the safe harbor. Catch the trade winds in your sails. Explore. Dream. Discover.
pink:Pink4sPig$$
```
cool we got some creds, let's ftp using this creds.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnhub/color]
└─$ ftp 192.168.0.103
Connected to 192.168.0.103.
220 (vsFTPd 3.0.3)
Name (192.168.0.103:n16hth4wk): pink
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -al
229 Entering Extended Passive Mode (|||48238|)
150 Here comes the directory listing.
drwxr-xr-x    6 0        0            4096 Jan 27 22:44 .
drwxr-xr-x    6 0        0            4096 Jan 27 22:44 ..
drwx------    2 1127     1127         4096 Feb 11 19:55 green
drwx------    3 1000     1000         4096 Feb 11 19:18 pink
drwx------    2 1081     1081         4096 Feb 20 16:07 purple
drwx------    2 1039     1039         4096 Feb 11 19:56 red
226 Directory send OK.
ftp>
ftp> cd green
550 Failed to change directory.
ftp> cd green
550 Failed to change directory.
ftp> cd pink
250 Directory successfully changed.
ftp> ls 
229 Entering Extended Passive Mode (|||17085|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000           23 Feb 11 16:59 note.txt
226 Directory send OK.
ftp> get note.txt
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||6241|)
150 Opening BINARY mode data connection for note.txt (23 bytes).
100% |*************************************************************************************************************************|    23        1.27 KiB/s    00:00 ETA
226 Transfer complete.
23 bytes received in 00:00 (1.17 KiB/s)
ftp> put normal.tcp 
local: normal.tcp remote: normal.tcp
229 Entering Extended Passive Mode (|||56677|)
150 Ok to send data.
100% |*************************************************************************************************************************|  1281        9.25 MiB/s    00:00 ETA
226 Transfer complete.
1281 bytes sent in 00:00 (672.92 KiB/s)
ftp> ^D
221 Goodbye.
```
cool we logged in ftp as user `pink` try checking other dirs we could not. we only have access to `pink` dir and found a note.txt then download the file. let's check the content of note.txt and also we have file upload permission.









