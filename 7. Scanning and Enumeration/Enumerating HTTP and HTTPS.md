# Enumerating HTTP and HTTPS

Running Nmap on the target machine (Kioptrix) will give us the following results:

```bash
┌──(kali㉿kali)-[~]
└─$ nmap -T4 -p- -A 10.0.2.15
Starting Nmap 7.94 ( https://nmap.org ) at 2023-12-06 07:22 EST
Nmap scan report for 10.0.2.15
Host is up (0.00037s latency).
Not shown: 65529 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
| ssh-hostkey: 
|   1024 b8:74:6c:db:fd:8b:e6:66:e9:2a:2b:df:5e:6f:64:86 (RSA1)
|   1024 8f:8e:5b:81:ed:21:ab:c1:80:e1:57:a3:3c:85:c4:71 (DSA)
|_  1024 ed:4e:a9:4a:06:14:ff:15:14:ce:da:3a:80:db:e2:81 (RSA)
|_sshv1: Server supports SSHv1
80/tcp    open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1          32768/tcp   status
|_  100024  1          32768/udp   status
139/tcp   open  netbios-ssn Samba smbd (workgroup: VXMYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_64_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_ssl-date: 2023-12-06T17:23:02+00:00; +5h00m00s from scanner time.
|_http-title: 400 Bad Request
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2009-09-26T09:32:06
|_Not valid after:  2010-09-26T09:32:06
32768/tcp open  status      1 (RPC #100024)

Host script results:
|_clock-skew: 4h59m59s
|_smb2-time: Protocol negotiation failed (SMB2)
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.91 seconds
```

Initially, the two most important ports are 80 and 443, both of them are open and correspond to the Hypertext Transfer Protocol (HTTP). These ports can be opened in the browser to determine it’s contents for further enumeration. 

With that in mind, let open up the IP address we scanned with nmap in the browser:

![Kioptrix http page.png](Kioptrix_http_page.png)

The default webpage does not give us a lot of information, but clicking on the links or using certain browser extensions like [Wappalyzer](https://www.wappalyzer.com/), it is possible to determine the server version (i.e 1.3.20 )and the OS the server is running on (UNIX). It is a good practice to note down such things in a text editor of your choice.

We can take the enumeration a step further using a web vulnerability scanner, `nikto`. This tool comes bundled with  the Kali distribution. The basic syntax is `nikto -h {target ip}`

```bash
┌──(kali㉿kali)-[~]
└─$ nikto -h 10.0.2.15
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          10.0.2.15
+ Target Hostname:    10.0.2.15
+ Target Port:        80
+ Start Time:         2023-12-06 07:50:35 (GMT-5)
---------------------------------------------------------------------------
+ Server: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
+ /: Server may leak inodes via ETags, header found with file /, inode: 34821, size: 2890, mtime: Wed Sep  5 23:12:46 2001. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ /: Apache is vulnerable to XSS via the Expect header. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2006-3918
+ Apache/1.3.20 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ mod_ssl/2.8.4 appears to be outdated (current is at least 2.9.6) (may depend on server version).
+ OpenSSL/0.9.6b appears to be outdated (current is at least 3.0.7). OpenSSL 1.1.1s is current for the 1.x branch and will be supported until Nov 11 2023.
+ Apache/1.3.20 - Apache 1.x up 1.2.34 are vulnerable to a remote DoS and possible code execution.
+ Apache/1.3.20 - Apache 1.3 below 1.3.27 are vulnerable to a local buffer overflow which allows attackers to kill any process on the system.
+ Apache/1.3.20 - Apache 1.3 below 1.3.29 are vulnerable to overflows in mod_rewrite and mod_cgi.
+ mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell.
+ OPTIONS: Allowed HTTP Methods: GET, HEAD, OPTIONS, TRACE .
+ /: HTTP TRACE method is active which suggests the host is vulnerable to XST. See: https://owasp.org/www-community/attacks/Cross_Site_Tracing
+ ///etc/hosts: The server install allows reading of any system file by adding an extra '/' to the URL.
+ /usage/: Webalizer may be installed. Versions lower than 2.01-09 vulnerable to Cross Site Scripting (XSS). See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2001-0835
+ /manual/: Directory indexing found.
+ /manual/: Web server manual found.
+ /icons/: Directory indexing found.
+ /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
+ /test.php: This might be interesting.
+ /wp-content/themes/twentyeleven/images/headers/server.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpress/wp-content/themes/twentyeleven/images/headers/server.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wp-includes/Requests/Utility/content-post.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpress/wp-includes/Requests/Utility/content-post.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wp-includes/js/tinymce/themes/modern/Meuhy.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpress/wp-includes/js/tinymce/themes/modern/Meuhy.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /assets/mobirise/css/meta.php?filesrc=: A PHP backdoor file manager was found.
+ /login.cgi?cli=aa%20aa%27cat%20/etc/hosts: Some D-Link router remote command execution.
+ /shell?cat+/etc/hosts: A backdoor was identified.
+ /#wp-config.php#: #wp-config.php# file found. This file contains the credentials.
+ 8908 requests: 0 error(s) and 30 item(s) reported on remote host
+ End Time:           2023-12-06 07:50:57 (GMT-5) (22 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

The results contain important information such as outdated servers, misconfigurations and vulnerabilities, all these are important and should be noted down as well.

Next, we’ll use `dirbuster` to brute force the directories that were reported in the nikto scan. This is also known as a directory brute-force. This is a method used in penetration testing and ethical hacking to discover hidden directories or files on a web server. The goal is to find sensitive areas that may not be immediately visible but could potentially be vulnerable to attacks. This is typically achieved by systematically attempting to access common or known directories.

We can also use other tools like `gobuster` and `wfuzz` for directory brute-forcing but for the sake of the course, we’ll be going with dirbuster.

This tool can be run with a GUI using `dirbuster&` or through the shell `dirb {target ip}`. This example will use the GUI version:

![dirbuster.png](dirbuster.png)

1. Enter the target URL with the port number i.e 80
2. Set the number of threads to determine the scan speed. The higher the number, the faster the scan. Or just select the **Go Faster** option.
3. Allows you to select your own wordlist or use a default one.
4. Navigate to the directory of your preferred wordlist at `/usr/share/wordlists/dirbuster/` 
5. We can determine the file extensions we are looking for, in this case since it’s an Apache server which runs PHP, that is what we will be looking for.
6. Click the Start button to run the scan. 

**NOTE: It’s a good idea to also use Burp Suite to go through files and directories. You can also add the URL to your Scope to reduce noise. More information can be found [here](https://portswigger.net/burp/documentation/desktop/testing-workflow/test-scope#:~:text=To%20add%20a%20URL%20to%20your%20scope%3A%201,logging%20out-of-scope%20traffic.%20This%20can%20provide%20performance%20benefits.)**

**Opening the webpage, right-clicking and choosing “View page source” is also a good idea!**