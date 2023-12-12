# Scannning with Nmap

### What is Nmap?

Nmap, which stands for Network Mapper, is a powerful and versatile open-source tool used for network exploration, security scanning, and auditing. It is designed to discover hosts, services, and open ports on computer networks.

In Nmap, the results of a scan include information about the status of ports on the target system. The port states reported by Nmap are one of the tools most important features and includes the following:

1. **Open (Open):**
    - This means that Nmap successfully connected to the port, and the target system responded, indicating that the port is open and actively accepting connections.
2. **Closed (Closed):**
    - A closed port is one that Nmap was able to scan but received a response indicating that no service is running on that port.
3. **Filtered (Filtered):**
    - If Nmap is unable to determine whether a port is open or closed, it may report the port as filtered. This could be due to firewalls, packet filtering, or other network security mechanisms preventing Nmap from obtaining a clear response.
4. **Unfiltered (Unfiltered):**
    - An unfiltered port is one for which Nmap received no response during the scan. Nmap cannot determine if the port is open, closed, or filtered.
5. **Open|Filtered (Open|Filtered):**
    - This state indicates that Nmap was unable to determine if the port is open or filtered. This could occur when the target system is rate-limiting or blocking certain types of probes.
6. **Closed|Filtered (Closed|Filtered):**
    - Similar to Open|Filtered, this state indicates that Nmap was unable to determine if the port is closed or filtered.
7. **Unscanned (Unscanned):**
    - This state is used for ports that were not scanned during the Nmap operation. It might occur when certain ports are excluded from the scan or when the scan did not cover the full port range.
8. **Open|Filtered (Open|Filtered):**
    - This state indicates that Nmap was unable to determine if the port is open or filtered. This could occur when the target system is rate-limiting or blocking certain types of probes.

## Options & Syntax

Nmap’s basic syntax is:

`nmap [Scan Type(s)] [Options] {target specification}`

- **Scan Type(s):** Specify the type of scan you want to perform. This could be a TCP SYN scan, UDP scan, aggressive scan, etc.
- **Options:** Modify the behavior of the scan. Options can include settings for timing, verbosity, output format, and more.
- **Target Specification:** Define the target hosts or networks you want to scan. This can be an IP address, hostname, IP range, CIDR notation, or a combination.

## Nmap Scan Types

1. **TCP Connect Scan (-sT):**
    - Initiates a full TCP connection to each scanned port. This is the most basic form of scanning and is easy to detect.
        
        `nmap -sT target_ip`
        
2. **TCP SYN Scan (-sS):**
    - Also known as a "half-open" or "stealth" scan, it sends SYN packets to the target and listens for SYN-ACK responses to identify open ports.
        
        `nmap -sS target_ip`
        
3. **TCP ACK Scan (-sA):**
    - Sends TCP ACK packets to determine how a firewall handles them, providing information about firewall rules.
        
        `nmap -sA target_ip`
        
4. **UDP Scan (-sU):**
    - Scans for open UDP ports, which are commonly used for services like DNS and DHCP.
        
        `nmap -sU target_ip`
        
5. **TCP Window Scan (-sW):**
    - Similar to SYN scan but uses a zero TCP window size, which can bypass some firewall filters.
        
        `nmap -sW target_ip`
        
6. **TCP Maimon Scan (-sM):**
    - Completes the three-way handshake but sends a RST packet immediately after the handshake is completed.
        
        `nmap -sM target_ip`
        
7. **TCP Null Scan (-sN):**
    - Sends TCP packets with no flags set, relying on the target's response to identify open ports.
        
        `nmap -sN target_ip`
        
8. **TCP FIN Scan (-sF):**
    - Sends TCP FIN packets to identify open ports, exploiting the fact that some systems will not respond to these packets if the port is open.
        
        `nmap -sF target_ip`
        
9. **TCP Xmas Scan (-sX):**
    - Sends packets with the FIN, URG, and PUSH flags set, aiming to elicit responses from open ports.
        
        `nmap -sX target_ip`
        
10. **Idle Scan (-sI):**
    - Also known as a "zombie" or "spoofed" scan, it uses a third-party host as a "zombie" to hide the true source of the scan.
        
        `nmap -sI zombie_ip target_ip`
        

## Nmap Commands

### 1. Host Discovery

| Host Discovery Method | Option | Description |
| --- | --- | --- |
| Ping Scan | -sn | Disable port scanning, only perform host discovery |
| Host Discovery | -Pn | Discovers host on a network |
| TCP SYN Ping | -PS <port> | Send TCP SYN packets to specified port for discovery |
| TCP ACK Ping | -PA <port> | Send TCP ACK packets to specified port for discovery |
| ICMP Echo Request | -PE, -PP, -PM | Send ICMP Echo Requests, Timestamp, or Netmask |
| UDP Ping | -PU <port> | Send UDP packets to specified port for discovery |
| IP Protocol Ping | -PO <protocol> | Send IP protocol-specific packets for discovery |
| ARP Ping | --send-eth | Send ARP requests for discovery (local network) |
| Disable DNS Resolution | --system-dns, -n | Disable DNS resolution for discovered hosts |

### 2. Ports:

| Option | Description |
| --- | --- |
| -p- | Scan all 65535 TCP ports |
| -p<port> | Specify a single port |
| -p<start>-<end> | Specify a range of ports |
| -p<port1>,<port2>,... | Specify multiple ports separated by commas |
| -F | Fast scan, only scans the most common 100 ports |
| --top-ports <num> | Scan the top N most common ports |
| --exclude-ports <ports> | Exclude specified ports from the scan |

### 3. OS & Version Detection:

| Option | Description |
| --- | --- |
| -sV | Probe open ports to determine service/version info |
| --version-intensity <level> | Set version scan intensity (0-9, default is 7) |
| --version-light | Limit to most likely probes (equivalent to --version-intensity 2) |
| --version-all | Try all probes (equivalent to --version-intensity 9) |
| -O | Enable OS detection |
| --osscan-limit | Limit OS detection to promising targets |
| --osscan-guess | Guess OS detection results |
| --fuzzy | Enable version scan detection level 2 (fuzzy match) |

### 4. Timing and Performance:

| Option | Timing Template | Description |
| --- | --- | --- |
| -T0 | Paranoid | Very slow and careful, good for IDS evasion |
| -T1 | Sneaky | Slower than normal, avoids some IDS triggers |
| -T2 | Polite | Normal speed, prioritizes less disruptive scans |
| -T3 | Normal | Default timing template, a balance between speed and reliability |
| -T4 | Aggressive | Faster scan, may be more easily detected |
| -T5 | Insane | Very fast scan, may trigger IDS or network anomalies |

### 5. Port Specification:

| Option | Description | Example Usage |
| --- | --- | --- |
| -p- | Scan all 65535 TCP ports | nmap -p- target_ip |
| -p<port> | Specify a single port | nmap -p80 target_ip |
| -p<start>-<end> | Specify a range of ports | nmap -p20-100 target_ip |
| -p<port1>,<port2>,... | Specify multiple ports separated by commas | nmap -p80,443,8080 target_ip |
| -F | Fast scan, only scans the most common 100 ports | nmap -F target_ip |
| --top-ports <num> | Scan the top N most common ports | nmap --top-ports 10 target_ip |
| --exclude-ports <ports> | Exclude specified ports from the scan | nmap --exclude-ports 22 target_ip |

### Resources:

Here are some resources for more hands-on practice with Nmap

[Nmap](https://tryhackme.com/room/furthernmap)

[Nmap Live Host Discovery](https://tryhackme.com/room/nmap01)
