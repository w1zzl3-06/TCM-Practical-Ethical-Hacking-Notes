# Enumerating SMB

### What is SMB?

SMB stands for Server Message Block, and it is a network protocol used for providing shared access to files, printers, and other communication between nodes on a network. SMB operates at the Application Layer of the OSI model and facilitates communication between computers over a local area network (LAN), wide area network (WAN), or the Internet.

SMB operates over the Transmission Control Protocol (TCP) and NetBIOS or Direct Hosting over TCP (TCP/IP). It is important to note that, due to security concerns, newer versions of SMB are recommended over older ones, and organizations are encouraged to keep their systems updated to benefit from the latest security features.

## Enumeration

Based on the results of the nmap scan, it shows that SMB is open and running on port 139 giving us a chance to investigate further.

We can enumerate SMB shares through a variety of ways, one of the go to methods is to use the Metasploit Framework which is an open-source hacking tool for developing, testing, and executing exploits on a target system. It provides a comprehensive set of tools  to assess and enhance the security of computer systems by identifying vulnerabilities and testing for potential exploits.

Launch metasploit through the `msfconsole` in the terminal and search for an smb scanner with `search auxilliary/scanner/smb_version`:

![Kioptrix_smb_version_smb](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/0be0843f-5afa-4a1b-98aa-db225b615841)

We’ve successfully obtained the Samba version which would be very useful later.

Next, we can use `smbclient` to explore the shares present on the SMB server:

![SMBclient](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/3f0a1ed8-80f3-417d-8092-3cc7b1f01a87)

Now to access the shares:

![2023-12-08_14_37_48-Kali_Running_-_Oracle_VM_VirtualBox](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/0910f69c-ce40-47e3-b377-3afc326ade1e)

**Question**: My enum4linux and/or smbclient are not working. I am receiving "Protocol negotiation failed: NT_STATUS_IO_TIMEOUT". How do I resolve?

**Resolution**:

On Kali, edit /etc/samba/smb.conf

Add the following under global:

client min protocol = CORE

client max protocol = SMB3
