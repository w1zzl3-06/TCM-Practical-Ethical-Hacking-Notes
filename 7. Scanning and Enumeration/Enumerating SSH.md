# Enumerating SSH

### What is SSH?

SSH, or Secure Shell, is a cryptographic network protocol that provides a secure way to access and manage remote devices or servers over an unsecured network.

The nmap scan shows that SSH is running on port 22. At the enumeration phase, the goal is to simply try and connect to it on that specific port to get information on what is running:

![2023-12-08 15_02_00-Kali [Running] - Oracle VM VirtualBox.png](2023-12-08_15_02_00-Kali_Running_-_Oracle_VM_VirtualBox.png)

Unfortunately Kioptrix is an old machine and trying to connect the normal way gives us errors such as requiring a key-exchange method using `oKexAlgorithms=+`  and a host key using `-c`. Both these methods don’t seem to work. Luckily with a little bit of searching and help. Using the following syntax seemed to work:

`ssh [john@172.16.216.130](mailto:john@172.16.216.130) -oHostKeyAlgorithms=+ssh-dss -oKexAlgorithms=+diffie-hellman-group-exchange-sha1 -c aes256-cbc`

The login asks for a password which we do not have, but as previously stated the goal was to try and login to see if there’s any banner information that could be of use. In this case there wasn’t any. Guess that’s it for SSH…….for now.