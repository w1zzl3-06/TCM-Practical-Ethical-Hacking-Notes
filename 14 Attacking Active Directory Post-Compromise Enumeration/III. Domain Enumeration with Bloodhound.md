# III. Domain Enumeration with Bloodhound

Bloodhound is a popular penetration testing  tool that is designed to analyze the trust relationships, permissions, and group memberships within an AD environment to identify potential attack paths and security vulnerabilities.

Make sure the latest version of bloodhound is installed by using `sudo pip install bloodhound`.

Next use the following command `sudo neo4j console` as it is required to run bloodhound, this will give is a server address once it executes and a remote interface as well:

```bash
 Bolt enabled on localhost:7687.
 INFO  Remote interface available at http://localhost:7474/

```

Clicking on the remote interface link will open the neo4j browser:

![neo4j](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/acea6152-b35f-48d8-9b69-43b664587016)

On first use, use the following credentials to login `neo4j:neo4j`. We’ll be opted to change the credentials, please do and remember them.

![neo4j_1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/eab2e8c6-3977-479b-90ed-c38273c11f06)

Once that’s done, run bloodhound with `sudo bloodhound`

A login prompt will appear, use the neo4j credentials.

We need to fill bloodhound with data using a component called an ingestor. Open a new terminal, create a new directory (name it bloodhound or whatever), move into it and run ingestor with:

```bash
 sudo bloodhound-python -d DOMAIN -u <username> -p <password> -ns <DC-IP> -c all 
```

- -nc stands for nameserver which is the domain controller IP.
- -c stands for collect, in this case we’re collecting everything with the ‘all’ argument.

```bash
┌──(kali㉿kali)-[~/bloodhound]
└─$ sudo bloodhound-python -d ONEPIECE.local -u Franky -p Password1 -ns 10.0.2.8 -c all
[sudo] password for kali: 
INFO: Found AD domain: onepiece.local
INFO: Getting TGT for user
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (strawhats-pc.onepiece.local:88)] [Errno -3] Temporary failure in name resolution
INFO: Connecting to LDAP server: strawhats-pc.onepiece.local
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 3 computers
INFO: Connecting to LDAP server: strawhats-pc.onepiece.local
INFO: Found 11 users
INFO: Found 52 groups
INFO: Found 3 gpos
INFO: Found 2 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: USOPP.ONEPIECE.local
INFO: Querying computer: FRANKY.ONEPIECE.local
INFO: Querying computer: STRAWHATS-PC.ONEPIECE.local
INFO: Done in 00M 01S

```

Hit `ENTER`

Back to the bloodhound folder we created and there’s a bunch of json files.

```bash
┌──(kali㉿kali)-[~/bloodhound]
└─$ ls
20240503075944_computers.json   20240503075944_gpos.json    20240503075944_users.json
20240503075944_containers.json  20240503075944_groups.json
20240503075944_domains.json     20240503075944_ous.json
```

Back to the bloodhound tool, upload the json files by clicking here, going to the bloodhound folder and highlighting everything.

![bloodhound](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/4a0a797b-399c-416a-824b-fc0894663787)

Wait for it to finish uploading.

Click on the drop down menu to the left and go to `Analysis`:

![bloodhound_1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/530c838e-2e4a-49d9-8155-bc095ef345a7)

Here we can click on the options such as `Find all Domain Admins` and we should have a nice graphical representation of it. We can click on the nodes in the graphic for even more information. 

Feel free to go through everything and get accustomed to what this powerful tool is capable of.
