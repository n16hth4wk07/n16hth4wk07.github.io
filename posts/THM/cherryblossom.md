![image](https://user-images.githubusercontent.com/87468669/221467235-45ecff1c-40dc-4872-92d5-8a931b0b523f.png)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Mon Feb 27 04:42:49 2023 as: nmap -sC -sV -T4 -oN normal.tcp -p 22,139,445 -Pn 10.10.204.80
Nmap scan report for 10.10.204.80
Host is up (0.18s latency).

PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 21ee304ff8f79f326e4295f21a1a04d3 (RSA)
|   256 dcfcded6ec436100549b7c401e8f52c4 (ECDSA)
|_  256 1281256e0864f6eff50c58711838a5c6 (ED25519)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: UBUNTU; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: UBUNTU, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-02-27T03:43:03
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: cherryblossom
|   NetBIOS computer name: UBUNTU\x00
|   Domain name: \x00
|   FQDN: cherryblossom
|_  System time: 2023-02-27T03:43:03+00:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Feb 27 04:43:07 2023 -- 1 IP address (1 host up) scanned in 18.06 seconds
```

## SMB Enumeration 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ smbclient -L 10.10.204.80 -N             
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
        Anonymous       Disk      Anonymous File Server Share
        IPC$            IPC       IPC Service (Samba 4.7.6-Ubuntu)
Reconnecting with SMB1 for workgroup listing.
Anonymous login successful

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            UBUNTU

┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ smbclient //10.10.204.80/Anonymous -N 
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Feb 10 01:22:51 2020
  ..                                  D        0  Sun Feb  9 18:48:18 2020
  journal.txt                         N  3470998  Mon Feb 10 01:20:53 2020

                10253588 blocks of size 1024. 4680240 blocks available
smb: \> get journal.txt 
getting file \journal.txt of size 3470998 as journal.txt (528.4 KiloBytes/sec) (average 528.4 KiloBytes/sec)
smb: \>
```
cool we have anonymous access to the `Anonymous` share, logged in and saw a file `journal.txt`, downloaded the file into our machine. Checking the content of the file it seems to be a base64 encoded text very large text. 

![image](https://user-images.githubusercontent.com/87468669/221471172-decefadd-7706-4d13-988d-678e3a087ade.png)

using cyberchef to dcode the file, we downloaded the file back to our machine. 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ exiftool download.dat 
ExifTool Version Number         : 12.44
File Name                       : download.dat
Directory                       : .
File Size                       : 2.6 MB
File Modification Date/Time     : 2023:02:27 04:53:05+01:00
File Access Date/Time           : 2023:02:27 04:53:25+01:00
File Inode Change Date/Time     : 2023:02:27 04:53:16+01:00
File Permissions                : -rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 1280
Image Height                    : 853
Bit Depth                       : 8
Color Type                      : RGB
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
Image Size                      : 1280x853
Megapixels                      : 1.1
```
using exiftool to check the file type, we got to see that it is a PNG image file. 

![image](https://user-images.githubusercontent.com/87468669/221471845-1bcb2071-f144-4432-b010-597765d68e2e.png)

cool we got to see the image, let's try steganography to see what we can get from this image.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/…/THM/CheeryBlossom/stegpy/stegpy]
└─$ python steg.py ../../download.dat 
File _journal.zip succesfully extracted from ../../download.dat
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/…/THM/CheeryBlossom/stegpy/stegpy]
└─$ ls 
crypt.py  __init__.py  _journal.zip  lsb.py  lsb.pyc  __pycache__  steg.py  test.npy  tests.py
```
using stegpy, we are able to extract the content of the image file. we got a file `_journal.zip` cool let's extract the content.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ unzip _journal.zip     
Archive:  _journal.zip
file #1:  bad zipfile offset (local header sig):  0
```
trying to extract the content, got an error saying there's an issue with the header of the file. let's use zip magic bytes to correct the header.

![image](https://user-images.githubusercontent.com/87468669/221475414-fcbdc8f8-99b9-4747-a8bb-59b4b40fdba0.png)

we can see the header is not correct, let's correct it...

![image](https://user-images.githubusercontent.com/87468669/221475629-54ea454f-a3f0-4c37-b97c-3563454b4c26.png)

now we change it from `FF 4B FF 4B` to `50 4B 03 04`. save and exit.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ unzip _journal.zip
Archive:  _journal.zip
[_journal.zip] Journal.ctz password: 
   skipping: Journal.ctz             incorrect password
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ 
```
tried extracting the content again, found out there's  a password needed to extract it. `zip2john` to the rescue.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ zip2john _journal.zip > hash
ver 2.0 efh 5455 efh 7875 _journal.zip/Journal.ctz PKZIP Encr: TS_chk, cmplen=70461, decmplen=70434, crc=0B987D84 ts=0035 cs=0035 type=8
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ john -w=/usr/share/wordlists/rockyou.txt hash  
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
september        (_journal.zip/Journal.ctz)     
1g 0:00:00:00 DONE (2023-02-27 05:41) 2.631g/s 21557p/s 21557c/s 21557C/s 123456..whitey
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```
used `zip2john` to generate the password hash and crack it using `john` and boom we got the password. let's xtract the content of the zip file.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ unzip _journal.zip
Archive:  _journal.zip
[_journal.zip] Journal.ctz password: 
  inflating: Journal.ctz
```
now we extracted the content of the zip file, 🤔 this file looks lik a cherrytree file. let's open the file using cherrytree. 
When i tried opening it in cheerytree, it asked for a password which we dont have yet.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ file Journal.ctz
Journal.ctz: 7-zip archive data, version 0.4
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ 7z2john Journal.ctz > hash  
ATTENTION: the hashes might contain sensitive encrypted data. Be careful when sharing or posting these hashes
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ john -w=/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (7z, 7-Zip archive encryption [SHA256 128/128 SSE2 4x AES])
Cost 1 (iteration count) is 524288 for all loaded hashes
Cost 2 (padding size) is 5 for all loaded hashes
Cost 3 (compression type) is 2 for all loaded hashes
Cost 4 (data length) is 70283 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
tigerlily        (Journal.ctz)     
1g 0:00:05:53 DONE (2023-02-27 06:17) 0.002830g/s 15.84p/s 15.84c/s 15.84C/s ceejay..kirstie
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```
checking the file type, we can see it is a 7zip file. used `7zip2john` to generate the hash and crackd th hash using `john`, now we have the password let's extract the content of the 7zip file.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ 7z x Journal.ctz  

7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,8 CPUs Intel(R) Core(TM) i5 CPU       M 460  @ 2.53GHz (20655),ASM)

Scanning the drive for archives:
1 file, 70434 bytes (69 KiB)

Extracting archive: Journal.ctz
--
Path = Journal.ctz
Type = 7z
Physical Size = 70434
Headers Size = 146
Method = LZMA2:16 7zAES
Solid = -
Blocks = 1

    
Enter password (will not be echoed):
Everything is Ok  

Size:       158136
Compressed: 70434
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/THM/CheeryBlossom]
└─$ ls
download.dat  file.png  fulltcp.txt  hash  hashes  Journal.ctd  Journal.ctz  journal.txt  _journal.zip  normal.tcp  stego-toolkit  stegpy
```
cool we extracted the file `Journal.ctd` from it. cool let's open it on cherrytree.

reading through the contents of the cherrytree file,

```
Dear Diary,
   My therapist, Anitta, has asked me to start keeping a diary so that she can get a feel for how I'm feeling. I think it's bullshit, but here we are! I didn't want anyone reading you, so I'm keeping you hidden on the samba share. That should do the trick! My mother called today. Still thinks I should have been a lawyer. Yelled at her and she started crying, so there's another thing to feel guilty about. My girlfriend thinks mum's being too controlling, but she also told me off for being harsh. Talk about mixed signals! I have no idea what to do here. Maybe Anitta will be able to help, but we're not there yet. Anyway, I'd better get going. I have an idea for a big project that could revolutionise computer security! Going to start working on it tomorrow.
   Until next time,
      -J

Dear Diary,
   I spent most of today working on my new password list. These are passwords that no one has ever used before. Almost 10000 of them! I'm going to test it out tomorrow, but I think this could be the future! If we all start choosing passwords from this list, they are guaranteed to be unique, and will therefore be unbreakable. I've added it into my wordlists. Cutest name too: cherry-blossom.
   Yes, Diary. Today I am feeling good. I've done a lot of hard work, and I'm pleased how it's all turned out. Take that mum! I can be successful!
   Until tomorrow,
      -J

Dear Diary,
   Today I showed my girlfriend, Lily my unique password list and asked her to pick one for herself. That's two passwords allocated -- this is catching on! I wonder who to share it with next? I'll take it to see Anitta tomorrow. Mum doesn't deserve the gift -- maybe Harry? Haven't seen him in a while though, so maybe not. Anyway, I'm still feeling good. The demons haven't caught up with me yet! 
   Until tomorrow,
      -J

Dear Diary,
    As you know I spoke to Anitta today. She was very pleased with what I've been writing in you. Apparently three days was enough, but I'm to keep you to remind myself of how it feels to be happy. Who knows, maybe I'll keep writing in you anyway? I've been enjoying our chats! Probably won't be every day though. Things are going really well in my life right now. Lily is wonderful, and I might even try to get along more with my mother. To top it all off, the cherry trees have started to bloom; really early this year. What a beautiful time time to be alive!
    All the best,
       -J

```
reading throught the dairy, we got 2 usernames `lily, and anitta` also there was a password list in the diary, copied it to a file and let's brute force ssh password for these usernames.









