# V. Domain Enumeration with PingCastle

A PingCastle is a tool designed to assess quickly the Active Directory security level with a methodology based on risk assessment and a maturity framework.

Download PingCastle [here](https://www.pingcastle.com/).

This is a Windows application, so download the file onto a Windows user machine and run it (Make sure to run it with Admin privileges).

A cmd window should open. Select the first option `1-healthcheck-Score the risk of a domain` and press `ENTER`:

The tool will automatically detect the domain and the scan should take up to 3 - 5 minutes.

The finished scan should output a file `ad_hoc_onepiece.local`.  Clicking it opens a report that contain a comprehensive risk assessment of the Domain.