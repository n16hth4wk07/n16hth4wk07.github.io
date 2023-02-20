![image](https://user-images.githubusercontent.com/87468669/220096951-8e2942d4-d16d-43b8-8660-fd9da5f35f94.png)

first enumeration with nmap

```
# Nmap 7.93 scan initiated Mon Feb 20 12:45:17 2023 as: nmap -sC -sV -T4 -oN normal.tcp -p 3753,5900,5901,5092,5903 -Pn 10.0.30.92                                    
Nmap scan report for 10.0.30.92                                                                                                                                       
Host is up (0.17s latency).                                                                                                                                           
                                                                                                                                                                      
PORT     STATE  SERVICE    VERSION                                                                                                                                    
3753/tcp open   landesk-rc LANDesk remote management                                                                                                                  
5092/tcp closed magpie                                                                                                                                                
5900/tcp open   vnc?                                                               
| fingerprint-strings:                                                                                                                                                
|   GenericLines:                                                                  
|     Welcome to the jetbridge control                                             
|     Username: Password:                                                          
|     Invalid username                                                             
|     _____________________________                                                
|     you've been warned! >                                                        
|     -----------------------------                                                                                                                                   
|     .::!!!!!!!:.                                                                                                                                                    
|     .!!!!!:. .:!!!!!!!!!!!!                                                      
|     ~~~~!!!!!!. .:!!!!!!!!!UWWW$$$                                               
|     :$$NWX!!: .:!!!!!!XUWW$$$$$$$$$P                                             
|     $$$$$##WX!: .<!!!!UW$$$$" $$$$$$$$#                                                                                                                             
|     $$$$$ $$$UX :!!UW$$$$$$$$$ 4$$$$$*                                           
|     ^$$$B $$$$\x20 $$$$$$$$$$$$ d$$R"                                            
|     "*$bd$$$$ '*$$$$$$$$$$$o+#"                                                  
|     """" """""""                                                                                                                                                    
|     Welcome to the jetbridge control                                             
|     Username: Password:                                                          
|   GetRequest:                                                                    
|     Welcome to the jetbridge control                                                                                                                                
|     Username: Password:                                                          
|     Invalid username GET / HTTP/1.0                                              
|     _____________________________                                                
|     you've been warned! >                                                                                                                                           
|     -----------------------------                                                                                                                                   
|     .::!!!!!!!:.                                                                                                                                                    
|     .!!!!!:. .:!!!!!!!!!!!!                                                      
|     ~~~~!!!!!!. .:!!!!!!!!!UWWW$$$
|     .!!!!!:. .:!!!!!!!!!!!!                                                                                                                                [170/208]
|     ~~~~!!!!!!. .:!!!!!!!!!UWWW$$$                                                                                                                                  
|     :$$NWX!!: .:!!!!!!XUWW$$$$$$$$$P                                                                                                                                
|     $$$$$##WX!: .<!!!!UW$$$$" $$$$$$$$#                                                                                                                             
|     $$$$$ $$$UX :!!UW$$$$$$$$$ 4$$$$$*                                                                                                                              
|     ^$$$B $$$$\x20 $$$$$$$$$$$$ d$$R"                                                                                                                               
|     "*$bd$$$$ '*$$$$$$$$$$$o+#"                                                                                                                                     
|     """" """""""                                                                                                                                                    
|     Welcome to the jetbridge control                                                                                                                                
|     Username: Password:                                                                                                                                             
|   NULL:                                                                          
|     Welcome to the jetbridge control                                                                                                                                
|_    Username:                                                                    
5901/tcp open   vnc-1?                                                             
| fingerprint-strings:                                                             
|   DNSStatusRequestTCP:                                                           
|     ^@^L^@^@^P^@^@^@^@^@^@^@^@^@Enter decryption key:                            
|   DNSVersionBindReqTCP:                                                          
|     ^CEnter decryption key:                                                                                                                                         
|   GenericLines:                                                                                                                                                     
|     Enter decryption key:                                                        
|     /services/central-control.functions: line 72: syntax error near unexpected token `fi'
|     /services/central-control.functions: line 72: ` fi'                          
|     Pbageby Gbjre Bcrengvbaf                                                                                                                                        
|     Grezvany Bcrengvbaf                                                          
|     RKVG                                                                         
|   GetRequest:                                                                    
|     GET / HTTP/1.0                                                                                                                                                  
|     Enter decryption key:                                                        
|     /services/central-control.functions: line 72: syntax error near unexpected token `fi'
|     /services/central-control.functions: line 72: ` fi'                          
|     Pbageby Gbjre Bcrengvbaf                                                                                                                                        
|     Grezvany Bcrengvbaf                                                          
|     RKVG                                                                         
|   HTTPOptions:                                                                   
|     OPTIONS / HTTP/1.0                                                                                                                                              
|     Enter decryption key:                                                                                                                                           
|     /services/central-control.functions: line 72: syntax error near unexpected token `fi'                                                                           
|     /services/central-control.functions: line 72: ` fi'                          
|     Pbageby Gbjre Bcrengvbaf   
|     /services/central-control.functions: line 72: ` fi'                                                                                                    [132/208]
|     Pbageby Gbjre Bcrengvbaf                                                     
|     Grezvany Bcrengvbaf                                                          
|     RKVG                                                                                                                                                            
|   Help:                                                                                                                                                             
|     HELP                                                                                                                                                            
|     Enter decryption key:                                                                                                                                           
|     /services/central-control.functions: line 72: syntax error near unexpected token `fi'
|     /services/central-control.functions: line 72: ` fi'                          
|     Pbageby Gbjre Bcrengvbaf         
|     Grezvany Bcrengvbaf                                                          
|     RKVG                                                                         
|   NULL:                               
|     Enter decryption key:           
|   RTSPRequest:                                                                                                                                                      
|     OPTIONS / RTSP/1.0                                                           
|     Enter decryption key:                                                        
|     /services/central-control.functions: line 72: syntax error near unexpected token `fi'
|     /services/central-control.functions: line 72: ` fi'
|     Pbageby Gbjre Bcrengvbaf
|     Grezvany Bcrengvbaf    
|_    RKVG                                                                                                                                                            
5903/tcp open   vnc-3?                                                             
| fingerprint-strings:                                                             
|   GenericLines:                                                                                                                                                     
|     Welcome to a non-important service... (well, maybe there is something of essence somewhere)
|     have 10 seconds to find the correct menu and submenu
|     menu: Option 7       
|     submenu: Sub Option 2                                                                                                                                           
|     Option 1 4) Option 4 7) Option 7 10) Option 10 13) Quit                                                                                                         
|     Option 2 5) Option 5 8) Option 8 11) Option 11     
|     Option 3 6) Option 6 9) Option 9 12) Option 12
|     Choose option or press enter to reprint:                                                                                                                        
|     Option 1 4) Option 4 7) Option 7 10) Option 10 13) Quit                      
|     Option 2 5) Option 5 8) Option 8 11) Option 11
|     Option 3 6) Option 6 9) Option 9 12) Option 12
|     Choose option or press enter to reprint: 1) Option 1 4) Option 4 7) Option 7 10) Option 10 13) Quit                                                             
|     Option 2 5) Option 5 8) Option 8 11) Option 11                                                                                                                  
|     Option 3 6) Option 6 9) Option 9 12) Option 12                               
|     Choose option or press enter to reprint:
|     Option 3 6) Option 6 9) Option 9 12) Option 12                                                                                                          [94/208]
|     Choose option or press enter to reprint:                                     
|   NULL:                                                                          
|     Welcome to a non-important service... (well, maybe there is something of essence somewhere)                                                                     
|     have 10 seconds to find the correct menu and submenu                                                                                                            
|     menu: Option 7                                                                                                                                                  
|     submenu: Sub Option 2                                                                                                                                           
|     Option 1 4) Option 4 7) Option 7 10) Option 10 13) Quit                                                                                                         
|     Option 2 5) Option 5 8) Option 8 11) Option 11                               
|     Option 3 6) Option 6 9) Option 9 12) Option 12
|_    Choose option or press enter to reprint:                                     
3 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-s
ervice :                                
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port5900-TCP:V=7.93%I=7%D=2/20%Time=63F35D55%P=x86_64-pc-linux-gnu%r(NU                                                                                            
SF:LL,2C,"Welcome\x20to\x20the\x20jetbridge\x20control\r\nUsername:\x20")%         
SF:r(GetRequest,2AB,"Welcome\x20to\x20the\x20jetbridge\x20control\r\nUsern         
SF:ame:\x20Password:\x20\r\nInvalid\x20username\x20\x20GET\x20/\x20HTTP/1\                                                                                            
SF:.0\r\n\r\n\x20_____________________________\r\n<\x20Hey\x20hey\x20you'v
SF:e\x20been\x20warned!\x20>\r\n\x20-----------------------------\r\n\x20\
SF:x20\x20\x20\\\r\n\x20\x20\x20\x20\x20\\\r\n\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2                                                                                            
SF:0\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\.::!!!!!!!:\.\r\n\x20\x20\.!!         
SF:!!!:\.\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\         
SF:x20\x20\x20\x20\x20\x20\x20\x20\.:!!!!!!!!!!!!\r\n\x20\x20~~~~!!!!!!\.\                                                                                            
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\.:!                                                                                            
SF:!!!!!!!!UWWW\$\$\$\x20\r\n\x20\x20\x20\x20\x20\x20:\$\$NWX!!:\x20\x20\x
SF:20\x20\x20\x20\x20\x20\x20\x20\x20\.:!!!!!!XUWW\$\$\$\$\$\$\$\$\$P\x20\
SF:r\n\x20\x20\x20\x20\x20\x20\$\$\$\$\$##WX!:\x20\x20\x20\x20\x20\x20\.<!                                                                                            
SF:!!!UW\$\$\$\$\"\x20\x20\$\$\$\$\$\$\$\$#\x20\r\n\x20\x20\x20\x20\x20\x2                                                                                            
SF:0\$\$\$\$\$\x20\x20\$\$\$UX\x20\x20\x20:!!UW\$\$\$\$\$\$\$\$\$\x20\x20\
SF:x204\$\$\$\$\$\*\x20\r\n\x20\x20\x20\x20\x20\x20\^\$\$\$B\x20\x20\$\$\$
SF:\$\\\x20\x20\x20\x20\x20\$\$\$\$\$\$\$\$\$\$\$\$\x20\x20\x20d\$\$R\"\x2                                                                                            
SF:0\r\n\x20\x20\x20\x20\x20\x20\x20\x20\"\*\$bd\$\$\$\$\x20\x20\x20\x20\x         
SF:20\x20'\*\$\$\$\$\$\$\$\$\$\$\$o\+#\"\x20\r\n\x20\x20\x20\x20\x20\x20\x
SF:20\x20\x20\x20\x20\x20\x20\"\"\"\"\x20\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20\"\"\"\"\"\"\"\x20\r\nWelcome\x20to\x20the\x20jetbridge\x20control\r                                                                                            
SF:\nUsername:\x20Password:\x20")%r(GenericLines,29C,"Welcome\x20to\x20the                                                                                            
SF:\x20jetbridge\x20control\r\nUsername:\x20Password:\x20\r\nInvalid\x20us         
SF:ername\x20\r\n\r\n\x20_____________________________\r\n<\x20Hey\x20hey\
SF:20correct\x20menu\x20and\x20submenu\r\n\r\n\r\nmenu:\x20Option\x207\r\n
SF:submenu:\x20Sub\x20Option\x202\r\n1\)\x20Option\x201\x20\x20\x20\x20\x2
SF:04\)\x20Option\x204\x20\x20\x20\x207\)\x20Option\x207\x20\x20\x2010\)\x                                                                                            
SF:20Option\x2010\x20\x2013\)\x20Quit\r\n2\)\x20Option\x202\x20\x20\x20\x2         
SF:0\x205\)\x20Option\x205\x20\x20\x20\x208\)\x20Option\x208\x20\x20\x2011         
SF:\)\x20Option\x2011\r\n3\)\x20Option\x203\x20\x20\x20\x20\x206\)\x20Opti                                                                                            
SF:on\x206\x20\x20\x20\x209\)\x20Option\x209\x20\x20\x2012\)\x20Option\x20
SF:12\r\nChoose\x20option\x20or\x20press\x20enter\x20to\x20reprint:\x20\r\
SF:n\r\n\r\n\r\n1\)\x20Option\x201\x20\x20\x20\x20\x204\)\x20Option\x204\x
SF:20\x20\x20\x207\)\x20Option\x207\x20\x20\x2010\)\x20Option\x2010\x20\x2                                                                                            
SF:013\)\x20Quit\r\n2\)\x20Option\x202\x20\x20\x20\x20\x205\)\x20Option\x2         
SF:05\x20\x20\x20\x208\)\x20Option\x208\x20\x20\x2011\)\x20Option\x2011\r\         
SF:n3\)\x20Option\x203\x20\x20\x20\x20\x206\)\x20Option\x206\x20\x20\x20\x                                                                                            
SF:209\)\x20Option\x209\x20\x20\x2012\)\x20Option\x2012\r\nChoose\x20optio                                                                                            
SF:n\x20or\x20press\x20enter\x20to\x20reprint:\x201\)\x20Option\x201\x20\x
SF:20\x20\x20\x204\)\x20Option\x204\x20\x20\x20\x207\)\x20Option\x207\x20\
SF:x20\x2010\)\x20Option\x2010\x20\x2013\)\x20Quit\r\n2\)\x20Option\x202\x                                                                                            
SF:20\x20\x20\x20\x205\)\x20Option\x205\x20\x20\x20\x208\)\x20Option\x208\                                                                                            
SF:x20\x20\x2011\)\x20Option\x2011\r\n3\)\x20Option\x203\x20\x20\x20\x20\x
SF:206\)\x20Option\x206\x20\x20\x20\x209\)\x20Option\x209\x20\x20\x2012\)\
SF:x20Option\x2012\r\nChoose\x20option\x20or\x20press\x20enter\x20to\x20re                                                                                            
SF:print:");                                                                       
                                                                                   
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Feb 20 12:47:55 2023 -- 1 IP address (1 host up) scanned in 158.36 seconds 
```

