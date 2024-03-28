# I. LLMNR Poisoning

**LLMNR** (Link-Local Multicast Name Resolution.) is a protocol used in modern Windows operating systems for resolving the IP addresses of neighbouring network devices when the Domain Name System (DNS) is not available.

- Previously known as **NBT-NS** (NetBIOS Name Service)
- Key flaw is that it utilizes a user’s username and NTLMv2 hash when appropriately responded to.

![LLMNR_Poisoning](https://github.com/w1zzl3-06/TCM-Practical-Ethical-Hacking-Notes/assets/141921425/a793ee9b-b0d6-45e4-8898-f86a289a0143)

## How to perform LLMNR Poisoning?

**Step 1:** Capture traffic using the Responder tool.

**Step 2:** Crack the hash using a tool like hashcat or johntheripper.

## Resource:

[LLMNR Poisoning and how to prevent it](https://tcm-sec.com/llmnr-poisoning-and-how-to-prevent-it/)
