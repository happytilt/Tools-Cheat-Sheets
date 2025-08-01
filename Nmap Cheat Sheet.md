# Nmap Cheat Sheet

# Helpful flags

- `-sS`
    - Syn scan
- `-sU`
    - UDP scan
- `-O`
    - Operating System detection
- `-sV`
    - services (and their version) detection
- `-oA, oN, oG, oX`
    - Saves output to a file
    - different flags, different file format
- `-A`
    - Aggressive mode
    - service detection, operating system detection, a traceroute and common script scanning

- `-p #`
    - specify a port to scan
    - `-p 80` scans port 80
    - `-p #-#` scans range of ports; 
    `-p 1-1024`
- `--script`
    - run nmap scripts
- `-T#`
    - -T1 to -T5
    - timing/speed of scans, faster = more noise
    - `-v`
        - verbose mode
        - `-vv` for more verbosity
- `--top-ports #`
    - selects top ports (commonly used)
    - `--top-ports 20` selects top 20 ports

# Scan types

- TCP Connect Scans (`sT`)
    - Full 3 way handshake
    - open = session made
    - close = port replies with RST
    - filtered = no response
    - noisy, easy to detect
    - doesn’t need sudo
- SYN Scans/Stealth Scans (`sS`)
    - half-open; doesn’t complete session
    - open = syn-ack reponse
    - close = RST reponse
    - filtered = no response
    - Stealthy, fast, efficient
    - needs sudo
- UDP Scans (`sU`)
    - open= udp reply or no response
    - closed = icmp unreachable
    - filtered = no reponse
    - finds services that tcp doesn’t find
    - slow, noisy, high false positives, requires root/admin privileges
- Ping Sweep (`-sn`)
    - ex: `nmap -sn 192.168.0.1-254` 
    `nmap -sn 192.168.0.0/24`
    - No port scan; ICMP echo packets or ARP requests on a local network
    - 

**For Firewall Evasion**

- TCP Null Scans (`sN`)
    - sends tcp packet with no flags; NULL
    - tests systems’ non-standard tcp behavior; best against older unix
    - open= udp reply or no response
    - closed = icmp unreachable
    - filtered = no reponse
    - Quiet, can bypass some filters
- TCP FIN Scans (`sF`)
    - TCP packet with only FIN flag
    - detecting closed ports, best with unix/linux systems
    - open= udp reply or no response
    - closed = icmp unreachable
    - filtered = no reponse
    - Low-profile, good for evading detection
- TCP Xmas Scans (`sX`)
    - TCP packet with FIN, PSH, and URG
    - tests open/closed port behavior
    - open= udp reply or no response
    - closed = icmp unreachable
    - filtered = no reponse
    - Quiet, may evade detection

NOTE: Windows replies with RST for these three scans above