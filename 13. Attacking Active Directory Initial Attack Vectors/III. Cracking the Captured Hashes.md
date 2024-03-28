# III. Cracking the Captured Hashes

So, we’ve gotten some NTLM hashes through responder. Lets copy any NTLMv2-SSP Hash and save it into a file using the following command:

```bash
┌──(kali㉿kali)-[~]
└─$ nano hashes.txt                            
```

Paste in the hash and press `CTRL + O` to save and `CTRL + X` to exit.

Next, we’ll use a tool called `hashcat` to crack the password. This comes bundled with Kali so it should work out of the box. We can also use another tool called `JohnTheRipper` , it all comes down to preference.

**NOTE:** It is generally not advisable to crack passwords using a virtual machine due to how slow and resource intensive it can be (VMs run off the system CPU but ideally we’d want to use a GPU for password cracking). If you have a system that uses a GPU, you can download hashcat for your base OS and run it there to speed up the process.

Okay so we can use the following command to check for the module we can use to crack the NTLM V2 hash:

```bash
hashcat --help | grep NTLM                             
   5500 | NetNTLMv1 / NetNTLMv1+ESS                                  | Network Protocol
  27000 | NetNTLMv1 / NetNTLMv1+ESS (NT)                             | Network Protocol
   5600 | NetNTLMv2                                                  | Network Protocol
  27100 | NetNTLMv2 (NT)                                             | Network Protocol
   1000 | NTLM                                                       | Operating System 
```

Ours is a NTLMv2 hash so the module number is 5600. So the command should be:

```bash
 hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt

```

**NOTE:** If it doesn’t work, try appending `--force` or `-O` to the command. It might still not work due to system constraints, but this is all for learning purposes and it should work with a more beefier system.

If everything works well though, we should have our cracked password, `Password1`.

![hahscat1.png](III%20Cracking%20the%20Captured%20Hashes%2066a4ef02229d4a869c8b2e3876da97e9/hahscat1.png)