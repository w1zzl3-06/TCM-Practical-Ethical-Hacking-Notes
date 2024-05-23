# IV. Domain Enumeration with Plumhound

[Plumhound](https://github.com/PlumHound/PlumHound) is a way too further utilize Bloodhound for Blue and Purple Teams. To use it, clone the repo using the `git clone` command:

```bash
sudo sudogit clone https://github.com/PlumHound/PlumHound.git
```

Move into the Plumhound directory and install the tool with:

```bash
sudo pip3 install -r requirements.txt
```

Once that’s done. Remember we can always get more information on how to use the tool with the `--help` command i.e `Plumhound --help` or by checking the github repository.

Make sure bloodhound is up and running before moving to the next step as Plumhound will pull all its information from there.

Let’s test it out:

```bash
sudo python3 PlumHound.py --easy -p <neo4j password >
```

Run it and check if it is able to successfully retrieve info:

![plumhound1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/e3916c3d-3ed6-49ce-b6e1-775a870e989b)

Cool. Now time to run it for real to execute a default task (read more about this and other capabilities on the repo):

```bash
sudo python3 PlumHound.py -x tasks/default.tasks -p <neo4j password>
```

Run it. This will compress everything into a `Reports.zip` file in the `/reports` directory.

Move into the `reports` directory and check the contents, there’s a bunch of files here with information similar to the ones we got from `ldapdomaindump` and Bloodhound. There’s an `index.html` file that can give a full report of everything contained in the directory. 

![plumhound2](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/e0af0cbb-ef1c-4390-bae0-3d378cf61051)

