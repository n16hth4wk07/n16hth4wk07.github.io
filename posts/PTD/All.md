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

let's pop reverse shell 






