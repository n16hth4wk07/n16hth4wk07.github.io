---
layout: default
title : n16hth4wk - picoCTF 2023 Writeup
---

### CTF Overview

picoCTF is a computer security game targeted at middle and high school students. The game consists of a series of challenges centered around a unique storyline where participants must reverse engineer, break, hack, decrypt, or do whatever it takes to solve the challenge. The challenges are all set up with the intent of being hacked, making it an excellent, legal way to get hands-on experience.

The Competition involves trying to solve a designated number of problems within a specified timeframe. The Competition begins on 14 March 2023 at 12:00pm Eastern Daylight Time (17:00 Monday, West Africa Time WAT) and continues until 28 March 2023 at 3:00pm Eastern Daylight Time (20:00 Monday, West Africa Time WAT) (the "Competition Period").

Enough with the talk let's get down to pwning 😉

## General Skill challenge solved by nighthawk

### money-ware - 300pts

![image](https://user-images.githubusercontent.com/87468669/228119891-1c5bfbec-bf10-4ed3-b1c0-35f6d5b4108f.png)

hmmmm we got a bitcoin address `1Mz7153HMuxXTuR2R1t78mGSdzaAtNbBWX` and we are asked to identify what scam the address was used to for. 

![image](https://user-images.githubusercontent.com/87468669/228120610-c9fcc611-e50b-445c-891c-43f36b0ba090.png)

after a few research i found a site [scam](https://scam-alert.io/) to info about this scam address. 

![image](https://user-images.githubusercontent.com/87468669/228120946-3a7bb4b5-c0d0-4ae7-8bf9-593ac8b91f58.png)

and boom we got it... The payment address for the `PETYA` ransomware scam.

final flag - picoCTF{Petya}


## Reverse Engineering challenge solved by nighthawk

### Reverse - 100pts 

![image](https://user-images.githubusercontent.com/87468669/228121406-7d1c9cc3-e051-425f-b7f8-11045ce1d7d6.png)

we are asked to reverse a file 🤔... let's download this file 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[/tmp]
└─$ wget https://artifacts.picoctf.net/c/273/ret                    
--2023-03-28 04:38:06--  https://artifacts.picoctf.net/c/273/ret
Resolving artifacts.picoctf.net (artifacts.picoctf.net)... 13.224.81.15, 13.224.81.92, 13.224.81.5, ...
Connecting to artifacts.picoctf.net (artifacts.picoctf.net)|13.224.81.15|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 16888 (16K) [application/octet-stream]
Saving to: ‘ret’

ret                                       100%[====================================================================================>]  16.49K  --.-KB/s    in 0s      

2023-03-28 04:38:08 (32.4 MB/s) - ‘ret’ saved [16888/16888]
```
downloaded the file using wget.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[/tmp]
└─$ file ret     
ret: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=64856d07d138e412faf30b9722d92f507e3b5c9c, for GNU/Linux 3.2.0, not stripped
```
checked what type of file it is using file, we can see it is not stripped so let's use strings to make the contents readable.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[/tmp]
└─$ strings ret     
[***]
picoCTF{H
3lf_r3v3H
r5ing_suH
cce55fulH
_d7b14d4H
[]A\A]A^A_
Enter the password to unlock this file: 
You entered: %s
Password correct, please see flag: picoCTF{3lf_r3v3r5ing_succe55ful_d7b14d43}
Access denied
:*3$"
GCC: (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0
crtstuff.c
deregister_tm_clones
[***]
```
and boom we got the flag.

final flag - picoCTF{3lf_r3v3r5ing_succe55ful_d7b14d43}


## Forensics challenge solved by nighthawk

### Invisible WORDs - 300pts 

![image](https://user-images.githubusercontent.com/87468669/228126206-783a2a5f-caca-4c59-b4a0-2e6020d166e4.png)

we got an image file, let's download it.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[/tmp]
└─$ wget https://artifacts.picoctf.net/c/405/output.bmp
--2023-03-28 05:08:58--  https://artifacts.picoctf.net/c/405/output.bmp
Resolving artifacts.picoctf.net (artifacts.picoctf.net)... 13.224.81.5, 13.224.81.15, 13.224.81.40, ...
Connecting to artifacts.picoctf.net (artifacts.picoctf.net)|13.224.81.5|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2073738 (2.0M) [application/octet-stream]
Saving to: ‘output.bmp’

output.bmp                                100%[====================================================================================>]   1.98M  2.45MB/s    in 0.8s    

2023-03-28 05:09:00 (2.45 MB/s) - ‘output.bmp’ saved [2073738/2073738]
```
let's analyse this file


