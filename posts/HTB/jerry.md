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

