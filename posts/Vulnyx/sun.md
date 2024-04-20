## FIrst enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Sun Apr 14 08:46:48 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80,139,445,8080 -Pn 172.16.246.131
Nmap scan report for 172.16.246.131
Host is up (0.00074s latency).

PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey: 
|   256 a9:a8:52:f3:cd:ec:0d:5b:5f:f3:af:5b:3c:db:76:b6 (ECDSA)
|_  256 73:f5:8e:44:0c:b9:0a:e0:e7:31:0c:04:ac:7e:ff:fd (ED25519)
80/tcp   open  http        nginx 1.22.1
|_http-title: Sun
|_http-server-header: nginx/1.22.1
139/tcp  open  netbios-ssn Samba smbd 4.6.2
445/tcp  open  netbios-ssn Samba smbd 4.6.2
8080/tcp open  http        nginx 1.22.1
|_http-title: Sun
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: nginx/1.22.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: SUN, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: -2s
| smb2-time: 
|   date: 2024-04-14T07:47:11
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Apr 14 08:47:13 2024 -- 1 IP address (1 host up) scanned in 25.22 seconds
```


## Enumerating SMB 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnyx/Sun]
└─$ smbclient -L 172.16.246.131 -N 

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        IPC$            IPC       IPC Service (Samba 4.17.12-Debian)
        nobody          Disk      File Upload Path
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
Protocol negotiation to server 172.16.246.131 (for a protocol between NT1 and NT1) failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available
```
checking for anonymous shares available for us, we can see that the share `nobody` is available and it says a file upload path. 




