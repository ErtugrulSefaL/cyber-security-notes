---
Main Topic: Cryptography
Sub Topic: Metasploit___Scanning
Links:
  - https://tryhackme.com/room/metasploitexploitation?taskNo=2&sharerId=6702abe0f77a70c90629f7db
Date: 18-11-2025
Related Notes:
  - "[[Metasploit___Scanning]]"
Tags:
  - Cyber_Security
  - THM
  - Tools
  - Exploitation
---

# Metasploit___Scanning

## Port Scanning
Metasploit has a number of modules to scan open ports on the target system and network. You can list potential port scanning modules available using the `search portscan` command.

![[Pasted image 20251118125043.png|1100]]

---

> [!Note] About port scanning
> As for information gathering, if your engagement requires a speedier approach to port scanning, Metasploit may not be your first choice. However, a number of modules make Metasploit a useful tool for the scanning phase. But for speedier scanning you can directly perform [[Nmap]] scans from the msfconsole prompt.

---
## Some of  these methods

### UDP service Identification
The **`scanner/discovery/udp_sweep`** module will allow you to quickly identify services running over the UDP (User Datagram Protocol). As you can see below, this module will not conduct an extensive scan of all possible UDP services but does provide a quick way to identify services such as DNS or NetBIOS.

![[Pasted image 20251118130514.png|525]]
*(I don't know why it's not working but if I don't forget I fix it and replace.)*

---
### SMB Scans
Metasploit offers several useful auxiliary modules that allow us to scan specific services. Below is an example for the SMB. Especially useful in a corporate network would be **`smb_enumshares`** and **`smb_version`** but please spend some time to identify scanners that the Metasploit version installed on your system offers.

![[Pasted image 20251118131059.png|1600]]
