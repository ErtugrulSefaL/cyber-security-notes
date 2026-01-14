---
Main Topic: Tools
Sub Topic: Gobuster___Basics
Links:
  - https://github.com/OJ/gobuster
Date: 13-01-2026
Related Notes: 
tags:
  - Cyber_Security
  - Exploitation
  - Basics
  - Tools
  - THM
---

# Gobuster___Basics


> [!INFO] Gobuster
> **Gobuster** is a high-performance directory/file, DNS and virtual host brute-forcing tool written in Go. It's designed to be fast, reliable, and easy to use for security professionals and penetration testers.

## 🎯 Usage

Gobuster uses a mode-based approach. Each mode is designed for specific enumeration tasks:

```
gobuster [mode] [options]
```

### Getting Help

```
gobuster help                   # Show general help
gobuster help [mode]            # Show help for specific mode
gobuster [mode] --help          # Alternative help syntax
```

Enumerate directories and files on web servers.

**Basic Usage:**

```
gobuster dir -u https://example.com -w wordlist.txt
```

**Advanced Options:**

Discover subdomains through DNS resolution.

**Basic Usage:**

```
gobuster dns -do example.com -w wordlist.txt
```

**Advanced Options:**

```
# Use custom DNS server
gobuster dns -do example.com -w wordlist.txt -r 8.8.8.8:53

# Increase threads for faster scanning
gobuster dns -do example.com -w wordlist.txt -t 50
```

Discover virtual hosts on web servers.

**Basic Usage:**

```
gobuster vhost -u https://example.com --append-domain -w wordlist.txt
```

---
