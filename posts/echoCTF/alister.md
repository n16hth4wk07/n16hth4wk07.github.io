![image](https://github.com/user-attachments/assets/9d0af957-6bb6-4997-8355-99bdba0b7b11)

## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Sun Jul 14 18:58:24 2024 as: nmap -sCV -T4 -Pn -p 1337,5244 -oN service.txt 10.0.160.56
Nmap scan report for 10.0.160.56
Host is up (0.17s latency).

PORT     STATE SERVICE VERSION
1337/tcp open  http    nginx 1.18.0
|_http-server-header: nginx/1.18.0
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
5244/tcp open  unknown
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest, HTTPOptions: 
|     HTTP/1.0 200 OK
|     Content-Type: text/html
|     Date: Sun, 14 Jul 2024 17:58:32 GMT
|     <!DOCTYPE html>
|     <html lang="en" translate="no">
|     <head>
|     <script src="https://polyfill.io/v3/polyfill.min.js?features=String.prototype.replaceAll"></script>
|     <meta charset="utf-8" >
|     <meta name="viewport" content="width=device-width, initial-scale=1" >
|     <meta name="referrer" content="same-origin" >
|     <meta name="generator" content="AList V3" >
|     <meta name="theme-color" content="#000000" >
|     <meta name="google" content="notranslate" >
|     <script src="https://g.alicdn.com/IMM/office-js/1.1.5/aliyun-web-office-sdk.min.js"
|     async
|     ></script>
|     <link rel="shortcut icon"
|     type="image/ico"
|     href="https://cdn.jsdelivr.net/gh/alist-org/logo@main/logo.svg"
|     <title>AList</title>
|     <script>
|     window.ALIST = {
|     cdn: '',
|_    monaco_cdn: undefin
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jul 14 19:00:03 2024 -- 1 IP address (1 host up) scanned in 99.38 seconds

```


## Enumerating port 1337 












