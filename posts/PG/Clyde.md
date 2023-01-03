First we start enumeration with nmap 

`nmap -p- --min-rate 1000 -oN fulltcp.txt -v <IP> -Pn`

 ```
 # Nmap 7.93 scan initiated Tue Jan  3 20:35:01 2023 as: nmap -p- --min-rate 10000 -oN fulltcp.txt -v -Pn 192.168.108.68
Increasing send delay for 192.168.108.68 from 0 to 5 due to 11 out of 23 dropped probes since last increase.
Warning: 192.168.108.68 giving up on port because retransmission cap hit (10).
Increasing send delay for 192.168.108.68 from 640 to 1000 due to 11 out of 15 dropped probes since last increase.
Nmap scan report for 192.168.108.68
Host is up (0.22s latency).
Not shown: 65366 filtered tcp ports (no-response), 163 closed tcp ports (conn-refused)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
80/tcp    open  http
4369/tcp  open  epmd
15672/tcp open  unknown
65000/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Tue Jan  3 20:38:22 2023 -- 1 IP address (1 host up) scanned in 201.04 seconds
```

