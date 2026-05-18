---
Main Topic: Network_Security
Sub Topic: Nmap_Live_Host_Discovery_01
Links:
  - https://tryhackme.com/room/nmap01
  - https://www.iana.org/assignments/icmp-parameters/icmp-parameters.xhtml
  - https://github.com/royhills/arp-scan/wiki/arp-scan-User-Guide
  - https://nmap.org/
Date: 25-03-2026
Related Notes:
  - "[[Nmap]]"
Tags:
---

# Nmap_Live_Host_Discovery_01

You have learned how , ICMP, , and can detect live hosts by completing this room. Any response from a host is an indication that it is online. Below is a quick summary of the command-line options for that we have covered.

| Scan Type | Example Command |
| --- | --- |
| Scan | `sudo nmap -PR -sn MACHINE_IP/24` |
| ICMP Echo Scan | `sudo nmap -PE -sn MACHINE_IP/24` |
| ICMP Timestamp Scan | `sudo nmap -PP -sn MACHINE_IP/24` |
| ICMP Address Mask Scan | `sudo nmap -PM -sn MACHINE_IP/24` |
| TCP SYN Ping Scan | `sudo nmap -PS22,80,443 -sn MACHINE_IP/30` |
| ACK Ping Scan | `sudo nmap -PA22,80,443 -sn MACHINE_IP/30` |
| UDP Ping Scan | `sudo nmap -PU53,161,162 -sn MACHINE_IP/30` |

Remember to add `-sn` if you are only interested in host discovery without port-scanning. Omitting `-sn` will let Nmap default to scanning live hosts for ports.

| Option | Purpose |
| --- | --- |
| `-n` | no DNS lookup |
| `-R` | reverse- lookup for all hosts |
| `-sn` | host discovery only |
