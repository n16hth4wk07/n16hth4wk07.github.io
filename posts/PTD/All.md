## This note contains pwning from beginning to the end of every targets. 

![image](https://github.com/user-attachments/assets/249c6f61-b546-4373-b86e-49ac71dfa42a)

> first discover targets in the platform ranging from .10 - .254. I have script to look for targets in a subnet or multiple subnet

```python
import subprocess
import concurrent.futures


# List of network ranges to scan
networks = [
   "10.150.150.0/22"]


# Files to write live hosts and web hosts
output_file = "alive_hosts.txt"
web_hosts_file = "web_hosts.txt"


# Function to scan a single network and write results to a file
def scan_network(network):
   try:
       print(f"Scanning network: {network}")
       result = subprocess.run(
           ["fping", "-a", "-g", network],
           stdout=subprocess.PIPE,
           stderr=subprocess.PIPE,
           text=True
       )


       output = result.stdout.strip()
       if output:
           with open(output_file, "a") as f:
               f.write(f"Live hosts in {network}:\n{output}\n\n")
           print(f"Live hosts written to {output_file}")
           return output.splitlines()  # Return live hosts as a list
       else:
           print(f"No live hosts found in {network}")
           return []


   except Exception as e:
       print(f"Error scanning {network}: {e}")
       return []


# Function to run httpx on a single host
def run_httpx(host):
   try:
       print(f"Running httpx on: {host}")
       result = subprocess.run(
           ["httpx", "-title", "-sc", "-cl", host],
           stdout=subprocess.PIPE,
           stderr=subprocess.PIPE,
           text=True
       )


       output = result.stdout.strip()
       if output:
           with open(web_hosts_file, "a") as f:
               f.write(f"Results for {host}:\n{output}\n\n")
           print(f"Results for {host} written to {web_hosts_file}")


   except Exception as e:
       print(f"Error running httpx on {host}: {e}")


# Main function to run the network scan and httpx in parallel
def main():
   with open(output_file, "w") as f:
       f.write("Live Hosts Scan Results:\n\n")


   with open(web_hosts_file, "w") as f:
       f.write("Web Hosts Scan Results:\n\n")


   max_workers = 10  # Number of concurrent workers (threads)


   with concurrent.futures.ThreadPoolExecutor(max_workers=max_workers) as executor:
       futures = [executor.submit(scan_network, network) for network in networks]
       live_hosts = []
       for future in concurrent.futures.as_completed(futures):
           try:
               hosts = future.result()
               live_hosts.extend(hosts)
           except Exception as e:
               print(f"Error: {e}")


   print("Network scan complete. Results saved in", output_file)


   # Run httpx on live hosts in parallel
   if live_hosts:
       with concurrent.futures.ThreadPoolExecutor(max_workers=max_workers) as executor:
           httpx_futures = [executor.submit(run_httpx, host) for host in live_hosts]
           for future in concurrent.futures.as_completed(httpx_futures):
               try:
                   future.result()
               except Exception as e:
                   print(f"Error: {e}")


if __name__ == "__main__":
   main()

```


> Running the script to discover targets

![image](https://github.com/user-attachments/assets/df25bf2a-b4db-4811-950c-35f3c6342b72)

> Using fping to check for targets in the suibnet

```bash
fping -a -g 10.150.150.0/22 2>/dev/null > ips.txt 

┌──(n16hth4wk㉿n16hth4wk-sec)-[~/Documents/PTD]
└─$ cat ips.txt 
10.150.150.2
10.150.150.11
10.150.150.12
10.150.150.15
10.150.150.17
10.150.150.18
10.150.150.21
10.150.150.27
10.150.150.38
10.150.150.48
10.150.150.55
10.150.150.56
10.150.150.57
10.150.150.59
10.150.150.66
10.150.150.69
10.150.150.100
10.150.150.113
10.150.150.122
10.150.150.123
10.150.150.129
10.150.150.130
10.150.150.134
10.150.150.135
10.150.150.136
10.150.150.137
10.150.150.138
10.150.150.145
10.150.150.146
10.150.150.147
10.150.150.150
10.150.150.166
10.150.150.178
10.150.150.181
10.150.150.182
10.150.150.188
10.150.150.193
10.150.150.199
10.150.150.202
10.150.150.212
10.150.150.219
10.150.150.222
10.150.150.224
10.150.150.226
10.150.150.232
10.150.150.242
```




