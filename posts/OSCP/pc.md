## First enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Wed Feb 28 13:02:53 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,8000 -Pn 192.168.227.210
Nmap scan report for 192.168.227.210
Host is up (0.20s latency).

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 62:36:1a:5c:d3:e3:7b:e1:70:f8:a3:b3:1c:4c:24:38 (RSA)
|   256 ee:25:fc:23:66:05:c0:c1:ec:47:c6:bb:00:c7:4f:53 (ECDSA)
|_  256 83:5c:51:ac:32:e5:3a:21:7c:f6:c2:cd:93:68:58:d8 (ED25519)
8000/tcp open  http-alt ttyd/1.7.3-a2312cb (libwebsockets/3.2.0)
|_http-server-header: ttyd/1.7.3-a2312cb (libwebsockets/3.2.0)
|_http-title: ttyd - Terminal
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 Not Found
|     server: ttyd/1.7.3-a2312cb (libwebsockets/3.2.0)
|     content-type: text/html
|     content-length: 173
|     <html><head><meta charset=utf-8 http-equiv="Content-Language" content="en"/><link rel="stylesheet" type="text/css" href="/error.css"/></head><body><h1>404</h1></body></html>
|   GetRequest: 
|     HTTP/1.0 200 OK
|     server: ttyd/1.7.3-a2312cb (libwebsockets/3.2.0)
|     content-type: text/html
|     content-length: 677047
|     <!DOCTYPE html><html lang="en"><head><meta charset="UTF-8"><meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"><title>ttyd - Terminal</title><link rel="icon" type="image/png" href="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAcCAYAAAAAwr0iAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAA0xpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMDY3IDc5LjE1Nzc0NywgMjAxNS8wMy8zMC0yMzo0MDo0MiAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vb
|   Socks5, X11Probe: 
|     HTTP/1.0 403 Forbidden
|     server: ttyd/1.7.3-a2312cb (libwebsockets/3.2.0)
|     content-type: text/html
|     content-length: 173
|_    <html><head><meta charset=utf-8 http-equiv="Content-Language" content="en"/><link rel="stylesheet" type="text/css" href="/error.css"/></head><body><h1>403</h1></body></html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8000-TCP:V=7.94SVN%I=7%D=2/28%Time=65DF20F5%P=x86_64-pc-linux-gnu%r
SF:(GetRequest,2ACF,"HTTP/1\.0\x20200\x20OK\r\nserver:\x20ttyd/1\.7\.3-a23
SF:12cb\x20\(libwebsockets/3\.2\.0\)\r\ncontent-type:\x20text/html\r\ncont
SF:ent-length:\x20677047\r\n\r\n<!DOCTYPE\x20html><html\x20lang=\"en\"><he
SF:css\"/></head><body><h1>403</h1></b
SF:ody></html>");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Feb 28 13:03:52 2024 -- 1 IP address (1 host up) scanned in 59.03 seconds
```


## Enumerating port 8000

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/13964076-d7a2-4d12-873d-e104cfe793e8)

we notice a web server running on port `8000`, opening it on a browser, we got a web page with an ssh shell access on the web page. we are able to run commands 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/38a1db88-b978-4f3e-a637-7b5582ed798d)

first we cat our `id_rsa.pub` file in our attacker system. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/5a6f59d8-d329-4219-9020-0718a24cc14e)

put the attacker `id_rsa.pub` key into the `authorised_keys` in the `.ssh` dir of the target. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e73f4dca-0ac3-497b-9265-a26c7cdfd76b)

login ssh as `user`. 


## Privilege Escalation

```shell
user@pc:~$ ls -al /opt/
total 16
drwxr-xr-x  3 root root 4096 Aug 25  2023 .
drwxr-xr-x 19 root root 4096 Jun 15  2022 ..
drwx--x--x  4 root root 4096 Jun 28  2023 containerd
-rw-r--r--  1 root root  625 Aug 25  2023 rpc.py
user@pc:~$ 
```
in the `/opt/` dir we found a file `rpc.py`. 

```python
user@pc:/opt$ cat rpc.py 
from typing import AsyncGenerator
from typing_extensions import TypedDict

import uvicorn
from rpcpy import RPC

app = RPC(mode="ASGI")


@app.register
async def none() -> None:
    return


@app.register
async def sayhi(name: str) -> str:
    return f"hi {name}"


@app.register
async def yield_data(max_num: int) -> AsyncGenerator[int, None]:
    for i in range(max_num):
        yield i


D = TypedDict("D", {"key": str, "other-key": str})


@app.register
async def query_dict(value: str) -> D:
    return {"key": value, "other-key": value}


if __name__ == "__main__":
    uvicorn.run(app, interface="asgi3", port=65432)
user@pc:/opt$ 
```
checking the contents of the file, we can see it is running something like `asgi3`. 










