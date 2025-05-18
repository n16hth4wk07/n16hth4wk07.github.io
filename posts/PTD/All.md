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






