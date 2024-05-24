# II. Golden Ticket Attack Overview.

- When a krbtgt account has been compromised, the domain has been owned.
- We can request access to any resource or system on the domain.
- Golden ticketss == complete access to every machine.
- We can utilized mimikatz to obtain the information necessary to perform this attack.
- Once we have the SID and krbtgt hash, we can generate a ticket.
- With a Golden Ticket, we can now access other machines form the command line.