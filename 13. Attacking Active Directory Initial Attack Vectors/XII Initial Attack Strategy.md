# XII. Initial Attack Strategy

- Start with Mitm6 or Responder
    - Mitm6 should only be ran in 5-10 minute sprints in a real-world environment, any longer and it can take down the network!
    - While running Responder, make sure to generate some traffic from vulnerability scans (Nessus)
- If scans are taking too long, look for websites in scope (http_version)
- Look for default credentials on web logins
    - Printers
    - Jenkins
- Think outside the box (If all else fails, what else is available on the network i.e websites, default credentials etc. ). Dig, Dig, Dig, Enumeration is key.