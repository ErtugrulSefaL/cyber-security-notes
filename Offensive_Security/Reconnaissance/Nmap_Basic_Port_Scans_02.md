---
Main Topic: Network_Security
Sub Topic: Nmap_Basic_Port_Scans_02
Links:
Date: 25-03-2026
Related Notes:
Tags:
---

# Nmap_Basic_Port_Scans_02
This room covered three types of scans.

| Port Scan Type | Example Command |
| --- | --- |
| Connect Scan | `nmap -sT 10.112.186.45` |
| TCP SYN Scan | `sudo nmap -sS 10.112.186.45` |
| UDP Scan | `sudo nmap -sU 10.112.186.45` |


These scan types should get you started discovering running TCP and services on a target host.

| Option | Purpose |
| --- | --- |
| `-p-` | all ports |
| `-p1-1023` | scan ports 1 to 1023 |
| `-F` | 100 most common ports |
| `-r` | scan ports in consecutive order |
| `-T<0-5>` | \-T0 being the slowest and T5 the fastest |
| `--max-rate 50` | rate <= 50 packets/sec |
| `--min-rate 15` | rate >= 15 packets/sec |
| `--min-parallelism 100` | at least 100 probes in parallel |

