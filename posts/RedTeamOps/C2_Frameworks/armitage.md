## Armitage Setup

Armitage is a scriptable red team collaboration tool for Metasploit that visualizes targets, recommends exploits, and exposes the advanced post- exploitation features in the framework. It is pre-installed on kali or intsll using the methods below 

>using the apt install method

```shell
sudo apt install armitage 
```

>starting up msfdb server

```shell
sudo systemctl start postgresql 

sudo msfdb init
```

![image](https://github.com/user-attachments/assets/221daacf-ff08-4c67-98e5-6a6a3687278b)


>startup armitage

```shell
sudo armitage
```

![image](https://github.com/user-attachments/assets/12ed8bfa-f32a-4835-9791-0031fe52c395)

click yes to setup everything


>Armitage up and running

![image](https://github.com/user-attachments/assets/06a7173c-a315-4f44-a320-96b9d59b9b20)


>Doing a quick scan using nmap

![image](https://github.com/user-attachments/assets/d33f29f5-4b91-4fe1-99b3-860dbe70e55e)


>Using an eternalblue exploit for the target

![image](https://github.com/user-attachments/assets/9de5602a-b0bc-447f-8364-d337d0fd0486)

drag and drop exploit on target. it will run exploit automatically for you 


>Successfully exploit MS17-010

![image](https://github.com/user-attachments/assets/8cff83e1-749a-45a7-8523-1b227ff6e5f0)


### And that's how to install and use armitage 😉 use it for educational purpose.
