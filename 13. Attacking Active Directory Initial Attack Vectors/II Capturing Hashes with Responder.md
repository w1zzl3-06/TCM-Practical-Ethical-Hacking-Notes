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

![Responder 1.png](II%20Capturing%20Hashes%20with%20Responder%2019a57c207a1a4d4d967545439f88028b/Responder_1.png)

Next boot up one Windows user machine.

On the Windows machine, let’s simulate some traffic by going to file explorer and entering our Kali IP into the search bar.

![Responder 2.png](II%20Capturing%20Hashes%20with%20Responder%2019a57c207a1a4d4d967545439f88028b/Responder_2.png)

A login prompt should appear on the Windows machine. Now we go back to our Kali to check on Responder and there it is!! NTLM hashes for our Windows user.

![Responder 3.png](II%20Capturing%20Hashes%20with%20Responder%2019a57c207a1a4d4d967545439f88028b/Responder_3.png)

Next up. We’ll attempt to crack the hashes.