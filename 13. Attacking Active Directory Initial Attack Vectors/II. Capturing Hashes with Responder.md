# II. Capturing Hashes with Responder

We’ll be using our Kali machine for this.

Before we start, make sure that our Windows and Kali machines are both under the same subnet network.

Open up the terminal in our Kali box and run responder with elevated privileges using the following command:

```bash
sudo responder -I eth0 -dwPv
```

- **`d`**: This flag enables HTTP Digest authentication. This is useful for capturing NTLMv1 and NTLMv2 hashes.
- **`w`**: This flag enables the "Always Auth" feature, which forces clients to authenticate to the Responder service even if they weren't originally going to.
- **`P`**: This flag enables the HTTP NTLM Relay attack, which captures NTLM hashes from HTTP authentication attempts.
- **`v`**: This flag enables verbose output, providing more detailed information about the tool's activities.

Press `ENTER` and take note of the Kali IP address.

![Responder_1](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/86397ccb-e5c4-4e8d-9288-1a00efd3f3b1)

Next boot up one Windows user machine.

On the Windows machine, let’s simulate some traffic by going to file explorer and entering our Kali IP into the search bar.

![Responder_2](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/bb16cf67-1d75-4d92-9519-584e46b0461b)

A login prompt should appear on the Windows machine. Now we go back to our Kali to check on Responder and there it is!! NTLM hashes for our Windows user.

![Responder_3](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/f9386230-aff5-4479-9232-9c6b1f4ec0bf)

Next up. We’ll attempt to crack the hashes.
