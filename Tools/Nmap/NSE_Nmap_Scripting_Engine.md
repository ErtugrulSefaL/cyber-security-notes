# Nmap Scripting Engine (NSE) - Network Mapper

<details open>
<summary>📋 Note Metadata</summary>

<dl>
<dt><b>Main Topic</b></dt><dd>Tools</dd>
<dt><b>Date</b></dt><dd>2026-01-09</dd>
<dt><b>Resources</b></dt><dd>https://tryhackme.com/room/furthernmap?taskNo=10&sharerId=6702abe0f77a70c90629f7db</dd>
<dt><b>Related Notes</b></dt><dd>"Nmap"</dd>
<dt><b>Status</b></dt><dd>status/wip</dd>
<dt><b>Tags</b></dt><dd>#Tools #Nmap</dd>
</dl>

</details>

---

> The Nmap Scripting Engine (NSE) is a powerful feature of the Nmap network scanning tool that allows users to write and run scripts to automate tasks such as service detection, vulnerability assessment, and more.

## Overview

The Nmap Scripting Engine (NSE) is one of Nmap's most powerful features. It provides a rich library of scripts for automated network auditing, fingerprinting, enumeration and exploitation techniques. Scripts in NSE are written primarily in Lua and can be used to perform complex actions that go beyond traditional port scanning.

## Core Concepts

### What is NSE?
- **Nmap Scripting Engine** - The scripting framework that comes bundled with every Nmap installation
- Scripts are written in the [Lua](https://www.lua.org/) programming language
- Provides standardized interface for executing automated network tasks
- Can be used for:
  - Service and version detection
  - Vulnerability analysis
  - Network discovery
  - Enumeration of services, shares, etc.

## How NSE Works

### Architecture
1. **Script Database**: Contains thousands of scripts for various protocols
2. **Lua Interpreter**: Executes the scripts written in Lua language
3. **Nmap Interface**: Integrates script execution with network scanning capabilities
4. **Script Categories**: Scripts are categorized based on their functionality

### Script Categories
- **safe** - Won't affect the target
- **intrusive** - Not safe: likely to affect the target
- **vuln** - Scan for vulnerabilities
- **exploit** - Attempt to exploit a vulnerability
- **auth** - Attempt to bypass authentication for running services (e.g. Log into an FTP server anonymously)
- **brute** - Attempt to bruteforce credentials for running services
- **discovery** - Attempt to query running services for further information about the network (e.g. query an SNMP server)
- **dos** - Denial of service attacks (dangerous!)
- **fuzzers** - Fuzz testing tools
- **malware** - Malware detection
- **smb** - SMB/Samba specific scripts
- **snmp** - SNMP related scans and enumeration
- **sqli** - SQL injection testing
- **ssh** - SSH-related scripts

A more exhaustive list can be found on the [Nmap website](https://nmap.org/book/nse-usage.html).

## Using NSE

### Basic Commands

#### Run All Scripts Against Host:
```bash
nmap -sS -sV --script all <target>
```

#### Run Specific Script Category:
```bash
nmap -sS -sV --script vuln <target>
```

#### Run a Single Script:
```bash
nmap -sS -sV --script http-enum <target>
```

### Useful Options

| Option | Description |
|--------|-------------|
| `--script` or `-sC` | Enable default NSE scripts (same as using `--script default`) |
| `--script-args` | Pass arguments to scripts |
| `--script-trace` | Show all packets sent/received during script execution |
| `--script-help` | Show built-in help for a script |

### Command Format
```bash
nmap [scan-options] --script <category or name> <targets>
```

#### Scripts with Arguments
Some scripts require arguments (for example, credentials for an authenticated vulnerability). These are given with the `--script-args` switch:

```bash
nmap -p 80 --script http-put --script-args http-put.url='/dav/shell.php',http-put.file='./shell.php'
```

Arguments are separated by commas and connected to the corresponding script with periods (`nmap <script-name>.<argument>`).

## Searching for Scripts

Nmap stores its scripts on Linux at `/usr/share/nmap/scripts`. There are two ways to search for installed scripts:

1. **Using `script.db`** - a formatted text file listing filenames and categories:
   ```bash
   grep "ftp" /usr/share/nmap/scripts/script.db
   ```

2. **Using `ls`**:
   ```bash
   ls -l /usr/share/nmap/scripts/*ftp*
   ```

You can also browse the full [Nmap script library](https://nmap.org/nsedoc/) online.

## Installing New Scripts

A standard `sudo apt update && sudo apt install nmap` will refresh the bundled scripts. To install a script manually:

```bash
sudo wget -O /usr/share/nmap/scripts/<script-name>.nse https://svn.nmap.org/nmap/scripts/<script-name>.nse
nmap --script-updatedb
```

## Script Examples

### Commonly Used Scripts

#### HTTP Enumeration
- `http-enum`: Enumerate directories and files commonly found on web servers
- `http-methods`: Discover supported HTTP methods on the target server

#### SMB/Windows Enumeration
- `smb-enum-shares`: List SMB shares (user-readable)
- `smb-os-discovery`: Retrieve operating system information from SMB services
- `smb-vuln-ms17-010`: Check for EternalBlue vulnerability (CVE-2017-0144)

#### SNMP Enumeration
- `snmp-brute`: Brute force SNMP community strings
- `snmp-info`: Retrieve detailed information about SNMP server

### Advanced Script Usage
```bash
# Run specific script with arguments
nmap -p 80 --script http-enum --script-args http-enum.basepath=/test <target>

# Trace network communication during script execution
nmap -sV --script vuln --script-trace <target>
```

## Script Development

### Basic Lua Syntax in NSE Scripts
NSE scripts follow the same syntax as standard Lua with some added functions for Nmap.

Example of simple NSE script:
```lua
-- example_script.nse
description = [[Simple example script]]

categories = {"default", "safe"}
author = "Your Name"

action = function(host, port)
    return "This is a test script running on " .. host.ip
end
```

### Creating Custom Scripts
1. **Save new script** in Nmap's `scripts/` directory or user-defined location
2. **Test script syntax**: Run with `nmap --script-help <script-name>`
3. **Use it**: Execute normally through nmap scanning process

## Best Practices

### For Security Professionals:
- Always test scripts on targets you own
- Use NSE scripts for legitimate penetration testing activities only
- Be cautious when using intrusive or exploit scripts
- Understand the potential impact of running certain scripts
- Check compatibility with target firewalls/IDS systems

### Performance Considerations:
- Default script category is generally safe and fast to run
- Use appropriate verbosity levels (`--verbose` or `--debug`) for troubleshooting
- Avoid aggressive scanning if a firewall is present (may alert security systems)

## References

- [Nmap Official Documentation](https://nmap.org/book/man-nse.html)
- [NSE Library on Nmap.org](https://nmap.org/nsedoc/)
- [Nmap Scripting Engine Tutorials](https://nmap.org/book/nse.html)