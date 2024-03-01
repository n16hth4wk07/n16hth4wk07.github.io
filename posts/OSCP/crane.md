## First enumeration with nmap

```shell

```

## Enumerating port 80

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/dca21234-2ff8-4a8b-a2bd-4eea17d8bb17)

a web server is running on port `80`, openning it on a browser, we an see it is running `suitecrm` service. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/3461072b-28cf-4d22-a27a-f17a9ed764fe)

trying default creds `admin:admin` and we are able to login to the administrator panel. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/36d759a8-df8a-4025-8f03-e74215e60dd0)

checking for `suitecrm` exploit, we got an exploit on searchsploit. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a8722ae5-0423-4662-a5b6-38a8bb78ebc6)

the searchsploit exploit did not work, found another exploit on github. [exploit](https://github.com/manuelz120/CVE-2022-23940). 

