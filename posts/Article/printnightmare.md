---
layout: default
title : nighthawk - Printnightmare - Authenticated Privilege Esacalation
---

### Description
The vulnerability printnightmare is often found in Active Directory environment, we will look on how to exploit it instead of triggering RCE that gets flagged majority of the time. we will just add our low level user to local admin group

### CVE
(CVE-2021-1675 / CVE-2021-34527)

### Steps to exploit

>First using the nxc smb module `printnightmare` to find the vulnerable target.

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/GOAD]
└─$ nxc smb ips.txt -u jon.snow -p iknownothing -M printnightmare 
SMB         192.168.56.22   445    CASTELBLACK      [*] Windows 10 / Server 2019 Build 17763 x64 (name:CASTELBLACK) (domain:north.sevenkingdoms.local) (signing:False) (SMBv1:None)
SMB         192.168.56.11   445    WINTERFELL       [*] Windows 10 / Server 2019 Build 17763 x64 (name:WINTERFELL) (domain:north.sevenkingdoms.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         192.168.56.10   445    KINGSLANDING     [*] Windows 10 / Server 2019 Build 17763 x64 (name:KINGSLANDING) (domain:sevenkingdoms.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         192.168.56.22   445    CASTELBLACK      [+] north.sevenkingdoms.local\jon.snow:iknownothing 
SMB         192.168.56.11   445    WINTERFELL       [+] north.sevenkingdoms.local\jon.snow:iknownothing 
SMB         192.168.56.10   445    KINGSLANDING     [-] sevenkingdoms.local\jon.snow:iknownothing STATUS_LOGON_FAILURE 
PRINTNIG... 192.168.56.11   445    WINTERFELL       Vulnerable, next step https://github.com/ly4k/PrintNightmare
```
we can see that the ip `.11` is vulnerable. let's exploit away.


