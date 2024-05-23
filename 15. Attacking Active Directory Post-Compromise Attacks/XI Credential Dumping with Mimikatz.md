# XI. Credential Dumping with Mimikatz.

Make sure the Windows machine is on and logged in as a local user.

Get mimikatz [here](https://github.com/ParrotSec/mimikatz).

Git clone into directory of choice in Kali. We need these files that are found in the `x64` directory.

Start a python server in kali with `python3 -m http.server`

On Windows go to the browser and type in the Kali IP address.

We should have access to our Kali directories, click on the following files to download them.

![mimi_2](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/8d94f7da-bee6-4d0a-b9e1-3b88d48c382f)

The files should now be in the Downloads folder.

Open the command-line as administrator.

Move to the downloads folder and run mimikatz and set privilege mode to debug.

![mimi_3](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/1c8139b6-9c3e-462d-a26f-e621e60a3766)

Now we can run attacks.

Use the `sekurlsa::` command to list out the attacks.

![mimi_4](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/21c7db9c-b250-4892-940e-ba3379ae8749)

Let’s try to list all available account credentials on the machine using `sekurlsa::logonPasswords`.

![mimi 5.png](XI%20Credential%20Dumping%20with%20Mimikatz%20e382e964cf8e4b79a4b186735edb531f/mimi_5.png)

This will return the NTLM hashes and plaintext password of the users and local admins.
