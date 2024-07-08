#### First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Mon Jul  8 17:18:06 2024 as: nmap -p- --min-rate 1000 -oN full_tcp.txt -Pn -v 192.168.223.231
Nmap scan report for 192.168.223.231
Host is up (0.12s latency).
Not shown: 65531 filtered tcp ports (no-response)
PORT      STATE  SERVICE
22/tcp    open   ssh
80/tcp    open   http
3000/tcp  closed ppp
33017/tcp open   unknown

Read data files from: /usr/bin/../share/nmap
# Nmap done at Mon Jul  8 17:20:13 2024 -- 1 IP address (1 host up) scanned in 126.80 seconds
```
