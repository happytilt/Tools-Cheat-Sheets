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

# **N**map **S**cripting **E**ngine (NSE)

(written in Lua :0)

## `--script=<script-name>, <script-name> , <script-name>`

## Categories

- `safe`: Won't affect the target
- `intrusive`: Not safe: likely to affect the target
- `vuln`: Scan for vulnerabilities
- `exploit`: Attempt to exploit a vulnerability
- `auth`: Attempt to bypass authentication for running services (e.g. Log into an FTP server anonymously)
- `brute`: Attempt to brute force credentials for running services
- `discovery`: Attempt to query running services for further information about the network (e.g. query an SNMP server).

All categories: [https://nmap.org/book/nse-usage.html](https://nmap.org/book/nse-usage.html)

`--script-args <script-name>.<argument>`

- provides arguments for an Nmap script
- ex: `nmap -p 80 --script http-put --script-args http-put.url='/dav/shell.php',http-put.file='./shell.php'`

all scripts and corresponding arguments: [https://nmap.org/nsedoc/](https://nmap.org/nsedoc/)

`--script-help <script-name>`

- Help page for the script name supplied

## Script Locations

Default local Nmap scripts location on Linux: `/usr/share/nmap/scripts/`

- Searching example: `ls -l /usr/share/nmap/scripts/*ftp*`

Grep-able file: `/usr/share/nmap/scripts/script.db`

- Searching example `grep "safe" /usr/share/nmap/scripts/script.db`

## Downloading Scripts

Manually download specific scripts off Nmap website:
`sudo wget -O /usr/share/nmap/scripts/<script-name>.nse [https://svn.nmap.org/nmap/scripts/](https://svn.nmap.org/nmap/scripts/)<script-name>.nse`

Update script.db: `nmap --script-updatedb`

## **Firewall Evasion**

> *Windows default firewall blocks ICMP (ping). Nmap uses ping by default → host appears dead and won’t be scanned*
> 
- `-Pn` - skip ping, treat host as alive; takes longer if host is actually offline
    - Nmap can use ARP requests for local network
- `-f` - fragment packets to evade detection
- `-mtu <number>` - custom packet size (multiple of 8)
- `-scan-delay <time>ms` - delay between packets; helps on unstable networks or avoids time-based triggers
- `-badsum` - send invalid checksum packets to detect firewall/IDS presence
- `--data-length` - appends specified number of random bytes of data to packets sent during scan
