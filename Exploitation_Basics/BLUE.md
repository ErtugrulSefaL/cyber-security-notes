---
Main Topic: THM
Sub Topic: BLUE
Links:
  - https://tryhackme.com/room/blue
Date: 09-01-2026
Related Notes:
Tags:
  - Exploitation
  - THM
---

# BLUE

Target IP Adress : 10.81.148.68

My THM IP (Internal Virtual IP Address) : 192.168.138.113

```
nmap -sS --script=vuln 10.81.148.68 -vv           
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-10 01:05 +0300  
NSE: Loaded 105 scripts for scanning.  
NSE: Script Pre-scanning.  
NSE: Starting runlevel 1 (of 2) scan.  
Initiating NSE at 01:05  
Completed NSE at 01:05, 10.01s elapsed  
NSE: Starting runlevel 2 (of 2) scan.  
Initiating NSE at 01:05  
Completed NSE at 01:05, 0.00s elapsed  
Initiating Ping Scan at 01:05  
Scanning 10.81.148.68 [4 ports]  
Completed Ping Scan at 01:05, 0.11s elapsed (1 total hosts)  
Initiating Parallel DNS resolution of 1 host. at 01:05  
Completed Parallel DNS resolution of 1 host. at 01:05, 0.50s elapsed  
Initiating SYN Stealth Scan at 01:05  
Scanning 10.81.148.68 [1000 ports]  
Discovered open port 135/tcp on 10.81.148.68  
Discovered open port 139/tcp on 10.81.148.68  
Discovered open port 3389/tcp on 10.81.148.68  
Discovered open port 445/tcp on 10.81.148.68  
Discovered open port 49154/tcp on 10.81.148.68  
Discovered open port 49153/tcp on 10.81.148.68  
Discovered open port 49158/tcp on 10.81.148.68  
Discovered open port 49160/tcp on 10.81.148.68  
Discovered open port 49152/tcp on 10.81.148.68  
Completed SYN Stealth Scan at 01:05, 6.70s elapsed (1000 total ports)  
NSE: Script scanning 10.81.148.68.  
NSE: Starting runlevel 1 (of 2) scan.  
Initiating NSE at 01:05  
NSE Timing: About 88.59% done; ETC: 01:06 (0:00:04 remaining)  
NSE Timing: About 89.57% done; ETC: 01:06 (0:00:07 remaining)  
Completed NSE at 01:07, 86.74s elapsed  
NSE: Starting runlevel 2 (of 2) scan.  
Initiating NSE at 01:07  
Completed NSE at 01:07, 0.00s elapsed  
Nmap scan report for 10.81.148.68  
Host is up, received reset ttl 126 (0.081s latency).  
Scanned at 2026-01-10 01:05:34 +03 for 93s  
Not shown: 991 closed tcp ports (reset)  
PORT      STATE SERVICE       REASON  
135/tcp   open  msrpc         syn-ack ttl 126  
139/tcp   open  netbios-ssn   syn-ack ttl 126  
445/tcp   open  microsoft-ds  syn-ack ttl 126  
3389/tcp  open  ms-wbt-server syn-ack ttl 126  
49152/tcp open  unknown       syn-ack ttl 126  
49153/tcp open  unknown       syn-ack ttl 126  
49154/tcp open  unknown       syn-ack ttl 126  
49158/tcp open  unknown       syn-ack ttl 126  
49160/tcp open  unknown       syn-ack ttl 126  
  
Host script results:  
|_smb-vuln-ms10-054: false  
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED  
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED  
| smb-vuln-ms17-010:    
|   VULNERABLE:  
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)  
|     State: VULNERABLE  
|     IDs:  CVE:CVE-2017-0143  
|     Risk factor: HIGH  
|       A critical remote code execution vulnerability exists in Microsoft SMBv1  
|        servers (ms17-010).  
|              
|     Disclosure date: 2017-03-14  
|     References:  
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/  
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143  
|_      https://technet.microsoft.com/en-us/library/security/ms17-010.aspx  
  
NSE: Script Post-scanning.  
NSE: Starting runlevel 1 (of 2) scan.  
Initiating NSE at 01:07  
Completed NSE at 01:07, 0.00s elapsed  
NSE: Starting runlevel 2 (of 2) scan.  
Initiating NSE at 01:07  
Completed NSE at 01:07, 0.00s elapsed  
Read data files from: /usr/share/nmap  
Nmap done: 1 IP address (1 host up) scanned in 104.36 seconds  
          Raw packets sent: 1027 (45.164KB) | Rcvd: 1002 (40.120KB)
```

```
hashdump  
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```

Jon -> alqfna22
flag1.txt -> flag{access_the_machine}
flag2.txt -> flag{sam_database_elevated_access}
flag3.txt -> flag{admin_documents_can_be_valuable}