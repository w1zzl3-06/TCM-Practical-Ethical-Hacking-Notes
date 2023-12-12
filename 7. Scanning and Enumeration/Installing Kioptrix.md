# Installing Kioptrix

********************Kioptrix******************** is a vulnerable virtual machine, designed for practicing and learning penetration testing and ethical hacking skills. This VM was intentionally created with security vulnerabilities, allowing for expolitation in a controlled environment.

[Kioptrix Download](https://tcm-sec.com/kioptrix)

This VM can be installed on either Virtualbox/VMWare. The installation steps are pretty similar regardless of which one you use.

- Import to Hypervisor of choice (Double-click the Kioptrix.ova file)
- Can leave the RAM settings as default or change to 256MB(Anything lower will cause problems).
- Set the Network adapter settings to NAT
- The username and password is `john:TwoCows2`
- This is an old machine so the deafult commands for IP address discovery (i.e `ip a` and `ipconfig`) do not work. Use `ping` command instead.

![Kioptrix ping.png](Kioptrix_ping.png)

In above screenshot, the ping command was used together with a random ip address. This allows our current machine to display it’s own IP address when sending packets to the target IP. Our machine’s IP address being 10.0.2.15 in this case.

Refer to this [link](https://michelepariani.com/2020/11/06/efficient-kali-and-kioptrix-setup-on-virtualbox-for-scanning-and-enumeration/) on how to successfully configure Kioptrix to interact with your Kali machine over an isolated network (never expose intentionally vulnerable machines to the outside world!).