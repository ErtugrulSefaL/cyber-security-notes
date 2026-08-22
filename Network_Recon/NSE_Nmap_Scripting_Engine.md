# Nmap Scripting Engine (NSE) - Network Mapper

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
- **Auth** - Authentication-related scans
- **Brute** - Brute-force attack scripts 
- **Discovery** - Service discovery, host discovery scripts
- **Dos** - Denial of service attacks (dangerous!)
- **Exploitation** - Exploit modules
- **Fuzzers** - Fuzz testing tools
- **Intrusive** - Non-invasive scans
- **Legacy** - Older scripts maintained for compatibility
- **Malware** - Malware detection
- **Mobile** - Mobile protocols (Android, iOS)
- **Poison** - Poisoning techniques 
- **Proxy** - Proxy-related tests
- **Scanning** - Scanning related modules (scan for vulnerabilities)
- **SMB** - SMB/Samba specific scripts  
- **Snmp** - SNMP related scans and enumeration
- **SQLi** - SQL injection testing
- **SSH** - SSH-related scripts
- **Targets** - Target selection filters
- **Telnet** - Telnet session interaction
- **Vuln** - Vulnerability detection and assessment


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

### Command Format
```bash
nmap [scan-options] --script <category or name> <targets>
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
- `snmp-info`: Retrieve detailed information about SNMMP server

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
- Use appropriate verbosity levels (--verbose or --debug) for troubleshooting
- Avoid aggressive scanning if firewall is present (may alert security systems)

## References  

- [Nmap Official Documentation](https://nmap.org/book/man-nse.html)
- [NSE Library on Nmap.org](https://nmap.org/nsedoc/)
- [Nmap Scripting Engine Tutorials](https://nmap.org/book/nse.html)