# Researching Potential Vulnerabilities

The enumeration phase has provided us with valuable information that we can use to look for vulnerabilities as we move into the exploitation phase. 

Based on the notes so far:

 From the nikto scan: `mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell.`

Pretty self explanatory. We can use a buffer overflow attack to gain access remotely.

- Google `mod_ssl 2.8.4 exploit`
    - The result returns two exploits. Both the [old](https://www.exploit-db.com/exploits/764) and [new](https://github.com/heltonWernik/OpenLuck)  exploits allow for a remote shell through a remote buffer overflow
- We can also use the searchsploit command on Kali to look for exploits.
    - Using `searchsploit mod_ssl 2.8.4`gives us the following results:
    
    ```bash
    ┌──(kali㉿kali)-[~]
    └─$ searchsploit mod_ssl 2.8.4
    ---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
     Exploit Title                                                                                                                                            |  Path
    ---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
    Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow                                                                                      | unix/remote/21671.c
    Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (1)                                                                                | unix/remote/764.c
    Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)                                                                                | unix/remote/47080.c
    ---------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
    ```
    

- Next up we have Samba 2.2.1a.
    - Google `samba 2.2.1a exploit` , this leads to multiple results but the most interesting is the [trans2open CVE:2003-020](https://www.exploit-db.com/exploits/7)1 written C which is can be exploited manually after getting a shell.
    - Another option is to launch metasploit via `msfconsole` and use the trans2open module with `use exploit/linux/samba/trans2open`:
        
        ![2023-12-11 14_30_14-Kali [Running] - Oracle VM VirtualBox.png](2023-12-11_14_30_14-Kali_Running_-_Oracle_VM_VirtualBox.png)