# IV. LLMNR Poisoning Mitigation

## Disable LLMNR and NBT-NS.

- To disable LLMNR, select “Turn OFF Multicast Name Resolution” under `Local Computer Policy` > `Computer Configuration` > `Administrative Templates` > `Network` > `DNS Client` in the Group Policy Editor.
- To disable NBT-NS, navigate to `Network Conncections` > `Network Adapter Porperties` > `TCP/IPV4 Properties` > `Advanced tab` > WINS tab and select “Disable NetBIOS over TCP/IP”.

## If LLMNR/NBT-NS cannot be disabled, the best course of action is:

- Require Network Access Control
- Require strong user passwords (e.g., >14 characters in length and limit common word usage). The more complex and long the password, the harder it is for an attacker to crack the hash.