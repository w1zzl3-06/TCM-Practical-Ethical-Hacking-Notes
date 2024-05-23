# XI. Credential Dumping with Mimikatz.

Make sure the Windows machine is on and logged in as a local user.

Get mimikatz [here](https://github.com/ParrotSec/mimikatz).

Git clone into directory of choice in Kali. We need these files that are found in the `x64` directory.

Start a python server in kali with `python3 -m http.server`

On Windows go to the browser and type in the Kali IP address.

We should have access to our Kali directories, click on the following files to download them.

![mimi 2.png](XI%20Credential%20Dumping%20with%20Mimikatz%20e382e964cf8e4b79a4b186735edb531f/mimi_2.png)

The files should now be in the Downloads folder.

Open the command-line as administrator.

Move to the downloads folder and run mimikatz and set privilege mode to debug.

![mimi 3.png](XI%20Credential%20Dumping%20with%20Mimikatz%20e382e964cf8e4b79a4b186735edb531f/mimi_3.png)

Now we can run attacks.

Use the `sekurlsa::` command to list out the attacks.

![mimi 4.png](XI%20Credential%20Dumping%20with%20Mimikatz%20e382e964cf8e4b79a4b186735edb531f/mimi_4.png)

Let’s try to list all available account credentials on the machine using `sekurlsa::logonPasswords`.

![mimi 5.png](XI%20Credential%20Dumping%20with%20Mimikatz%20e382e964cf8e4b79a4b186735edb531f/mimi_5.png)

This will return the NTLM hashes and plaintext password of the users and local admins.