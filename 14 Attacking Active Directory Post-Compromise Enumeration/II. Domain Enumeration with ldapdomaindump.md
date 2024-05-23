# II. Domain Enumeration with ldapdomaindump

In a scenario where IPv6 is not possible in the network but we have a compromised account. ldapdomaindump can be used to enumerate the domain.

Open the terminal and create directory (give it the name of the domain or whatever):

```bash
┌──(kali㉿kali)-[~]
└─$ mkdir onepiece.local
                                                                                                 
┌──(kali㉿kali)-[~]
└─$ cd onepiece.local 

```

Move into the directory and use the following command to dump:

```bash
sudo ldapdomaindump ldaps://DC-IP -u 'DOMAIN\user' -p Password
```

This will dump all the information related to the domain in the current directory:

```bash
┌──(kali㉿kali)-[~/onepiece.local]
└─$ sudo ldapdomaindump ldaps://10.0.2.8 -u 'ONEPIECE\Franky' -p Password1          
[*] Connecting to host...
[*] Binding to host
[+] Bind OK
[*] Starting domain dump
[+] Domain dump finished
                                                                                                 
┌──(kali㉿kali)-[~/onepiece.local]
└─$ ls                     
domain_computers_by_os.html  domain_groups.html  domain_trusts.grep          domain_users.html
domain_computers.grep        domain_groups.json  domain_trusts.html          domain_users.json
domain_computers.html        domain_policy.grep  domain_trusts.json
domain_computers.json        domain_policy.html  domain_users_by_group.html
domain_groups.grep           domain_policy.json  domain_users.grep

```

We can check the contents of the dump by opening it in firefox.

```bash
┌──(kali㉿kali)-[~/onepiece.local]
└─$ firefox domain_users_by_group.html
```

There’s a plethora of useful information here, feel free to check them out.

NOTE: Installing pimpmykali messes up the python versions causing problems with the ldapdomain tool. Visit this [link](https://tecadmin.net/how-to-switch-python-version-in-ubuntu-debian/) to learn how to change default python versions