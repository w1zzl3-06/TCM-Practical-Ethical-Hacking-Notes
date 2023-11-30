# Hunting Subdomains

Hunting for subdomains is an essential aspect of security testing and reconnaissance. Subdomains can often be overlooked attack vectors, and discovering them can help identify potential security risks.

### Tools:

- Sublist3r: This is a an open-source subdomain enumeration tool designed for security researchers and penetration testers.
    - Can be installed by typing *`apt install sublist3r`* in a Linux terminal.
    - Typing `sublist3r -h` will display how the tool functions.
- [Crt.sh](http://Crt.sh) : This is a web service and search engine that is primarily used for examining SSL/TLS certificates and accessing certificate transparency logs.
    - Navigate to the website and use `%` for subdomains e.g %.tesla.com
- https://github.com/owasp-amass/amass: This is a tool that is designed for information gathering and external asset discovery. The repo has a full installation guide available.
    - e.g. Typing `amass enum -passive -d tesla.com` will scan all subdomains for tesla.com