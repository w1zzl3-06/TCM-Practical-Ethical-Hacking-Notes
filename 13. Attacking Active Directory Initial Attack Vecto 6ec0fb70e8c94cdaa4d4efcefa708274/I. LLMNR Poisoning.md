# I. LLMNR Poisoning

**LLMNR** (Link-Local Multicast Name Resolution.) is a protocol used in modern Windows operating systems for resolving the IP addresses of neighbouring network devices when the Domain Name System (DNS) is not available.

- Previously known as **NBT-NS** (NetBIOS Name Service)
- Key flaw is that it utilizes a user’s username and NTLMv2 hash when appropriately responded to.

![LLMNR Poisoning.png](I%20LLMNR%20Poisoning%2083121728af7e40f59905860e1a69ae42/LLMNR_Poisoning.png)

## How to perform LLMNR Poisoning?

**Step 1:** Capture traffic using the Responder tool.

**Step 2:** Crack the hash using a tool like hashcat or johntheripper.

## Resource:

[LLMNR Poisoning and how to prevent it](https://tcm-sec.com/llmnr-poisoning-and-how-to-prevent-it/)