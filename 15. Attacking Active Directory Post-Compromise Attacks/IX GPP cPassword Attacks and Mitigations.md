# IX. GPP/cPassword Attacks and Mitigations.

## Overview:

- Group Policy Preferences (GPP) allowed admins to create policies using embedded credentials.
- These credentials were encrypted and placed in a “cPassword”.
- The “cPassword” key was accidentally released allowing for decryption.
- Patched in MS14-025, but it doesn’t prevent previous uses.
- Still relevant on pentests despite being a fairly old vulnerability.

## How to perform the attack?

**With gpp-decrypt.** `gpp-decrypt <hash>`.

**With metapsloit(with credentials).** `use smb_enum_gpp`.

## Mitigation Strategies:

- PATCH! Fixed in KB2962486.
- Delete old GPP xml files stored in the SYSVOL.

## Resources:

[https://infosecwriteups.com/attacking-gpp-group-policy-preferences-credentials-active-directory-pentesting-16d9a65fa01a](https://infosecwriteups.com/attacking-gpp-group-policy-preferences-credentials-active-directory-pentesting-16d9a65fa01a)