---
Main Topic: Network
Sub Topic: Nmap
Links:
  - https://tryhackme.com/room/nmap
Date: 03-11-2025
Related Notes:
Tags:
  - Cyber_Security
  - Network
  - Tools
  - THM
---

## Nmap

>[!INFO] Nmap
>Nmap (Network Mapper) is a powerful open-source tool used for network discovery and security auditing. It was originally designed to discover hosts and services on a computer network, but over time, it has evolved into one of the most widely used tools for network scanning, vulnerability assessment, and security testing.

>[!WARNING]  sudo or root priveleges
>We are either running `nmap` as `root` or using `sudo` because we don’t want to restrict Nmap’s abilities with our account privileges.

---

Nmap uses multiple ways to specify its targets:

- IP range using `-`: If you want to scan all the IP addresses from 192.168.0.1 to 192.168.0.10, you can write `192.168.0.1-10`
- IP subnet using `/`: If you want to scan a subnet, you can express it as `192.168.0.1/24`, and this would be equivalent to `192.168.0.0-255`
- Hostname: You can also specify your target by hostname, for example, `example.thm`

---

| Option | Explanation |
| --- | --- |
| `-sL` | List scan – list targets without scanning |
| ***Host Discovery*** |  |
| `-sn` | Ping scan – host discovery only |
| ***Port Scanning*** |  |
| `-sT` | TCP connect scan – complete three-way handshake |
| `-sS` | TCP SYN – only first step of the three-way handshake |
| `-sU` | UDP Scan |
| `-F` | Fast mode – scans the 100 most common ports |
| `-p[range]` | Specifies a range of port numbers – `-p-` scans all the ports |
| `-Pn` | Treat all hosts as online – scan hosts that appear to be down |
| ***Service Detection*** |  |
| `-O` | OS detection |
| `-sV` | Service version detection |
| `-A` | OS detection, version detection, and other additions |
| ***Timing*** |  |
| `-T<0-5>` | Timing template – paranoid (0), sneaky (1), polite (2), normal (3), aggressive (4), and insane (5) |
| `--min-parallelism <numprobes>` and `--max-parallelism <numprobes>` | Minimum and maximum number of parallel probes |
| `--min-rate <number>` and `--max-rate <number>` | Minimum and maximum rate (packets/second) |
| `--host-timeout` | Maximum amount of time to wait for a target host |
| ***Real-time output*** |  |
| `-v` | Verbosity level – for example, `-vv` and `-v4` |
| `-d` | Debugging level – for example `-d` and `-d9` |
| ***Report*** |  |
| `-oN <filename>` | Normal output |
| `-oX <filename>` | XML output |
| `-oG <filename>` | `grep` -able output |
| `-oA <basename>` | Output in all major formats |