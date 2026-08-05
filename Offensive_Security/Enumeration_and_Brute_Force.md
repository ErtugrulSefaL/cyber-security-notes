**Main Topic:** Offensive_Security
**Sub Topic:** Enumeration_and_Brute_Force
**Date:** 20-07-2026
**Resources:** https://tryhackme.com/room/enumerationbruteforce
**Related Notes:**
Tags: #status/wip  

---
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > Enumeration_and_Brute_Force

---
# Enumeration_and_Brute_Force

### What is Enumeration?

Enumeration is **actively pulling information** from a target system. It's the bridge between recon (passive/semi-passive) and exploitation. The goal: gather services, versions, usernames, shared resources, misconfigurations — anything that expands the attack surface.

Difference from recon: recon answers "what exists" (like a port scan), enumeration answers "what's inside this service, how can it be leveraged" (SMB share contents, user list via SNMP, etc.).

### Enumeration Categories and Tools

|Service/Area|Tool|What it extracts|
|---|---|---|
|SMB|`enum4linux`, `enum4linux-ng`, `smbclient`, `smbmap`|Shares, users, groups, OS info, RID cycling|
|NFS|`showmount -e <ip>`|Exported directories|
|FTP|`nmap --script ftp-anon`, `ftp`|Anonymous access, banner version|
|SSH|`nmap --script ssh2-enum-algos`, `ssh-audit`|Version, supported algorithms|
|SNMP|`snmpwalk`, `onesixtyone`, `snmp-check`|Users/processes/network info via community string|
|DNS|`dnsrecon`, `dig axfr`, `fierce`|Zone transfer, subdomains|
|HTTP/Web|`gobuster`, `ffuf`, `feroxbuster`, `whatweb`, `nikto`|Directories/files, CMS, tech stack|
|LDAP|`ldapsearch`, `windapsearch`|Domain users, groups|
|SMTP|`smtp-user-enum`, `nmap --script smtp-enum-users`|User verification via VRFY/EXPN|
|RPC|`rpcclient`|Domain info via null session|

**Practical flow example (SMB):**

```bash
# Check anonymous/null session
smbclient -L //10.10.x.x/ -N

# Detailed enumeration
enum4linux-ng -A 10.10.x.x

# Connect to a share
smbclient //10.10.x.x/sharename -N
```

**Web enumeration (directory/file discovery):**

```bash
# ffuf - fast, flexible
ffuf -u http://10.10.x.x/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e .php,.txt,.bak

# gobuster
gobuster dir -u http://10.10.x.x -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt
```

### What is Brute Forcing?

Brute forcing is finding valid credentials at an authentication point (login form, SSH, FTP, etc.) via **wordlist-based trial and error**. Enumeration's output (valid usernames) typically feeds into this step — they're usually chained: enumerate usernames first, then brute force passwords against those accounts.

**Types of brute force attacks:**

- **Credential stuffing**: trying known user:pass pairs across different services
- **Password spraying**: trying one/few common passwords against many usernames (to avoid lockout)
- **Dictionary attack**: direct password attempts from a wordlist
- **Reverse brute force**: fixed password, variable username

### Tool Comparison

|Tool|Target Type|Parallel/Speed|Module Support|Maintained|Notes|
|---|---|---|---|---|---|
|Hydra|Network services (SSH, FTP, HTTP form, RDP, etc.)|High (adjustable threads)|50+ protocols|Yes|Most commonly used on THM, general purpose|
|Medusa|Network services|High|Fewer modules but stable|Yes|Hydra alternative, more reliable in some edge cases|
|John the Ripper|Offline hash cracking|Medium-High (CPU)|Wide format support (MD5, NTLM, bcrypt, etc.)|Yes|Used after obtaining a hash, not online brute force|
|Hashcat|Offline hash cracking|Very high (GPU)|Widest format support|Yes|Much faster than John if GPU available|
|ffuf/wfuzz|Web login forms|High|HTTP-focused|Yes|More flexible than Hydra for web forms (custom filtering)|

**Recommendation:** For network services (SSH, FTP, RDP) use **Hydra**; for web login forms, manually analyze form parameters and use **ffuf** (or Hydra's http-post-form module); for offline hash cracking, use **Hashcat** if you have a GPU, otherwise **John**.

**Practical commands:**

```bash
# SSH brute force
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://10.10.x.x

# Username list + password list combo
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt 10.10.x.x ssh

# HTTP POST form (Hydra)
hydra -l admin -P rockyou.txt 10.10.x.x http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid login"

# ffuf for web form brute force
ffuf -u http://10.10.x.x/login -w rockyou.txt \
  -X POST -d "username=admin&password=FUZZ" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -fc 401

# Hash cracking (John)
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Hashcat (NTLM example)
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
```

### Defensive Side

- **Account lockout policy**: lock after N failed attempts (but insufficient against password spraying alone — **rate limiting** and **anomaly detection** are preferred)
- **MFA**: largely neutralizes the practical value of brute forcing
- **Fail2ban**: automatic IP-based blocking for services like SSH/FTP
- **Disabling null session/anonymous access**: proper configuration of settings like `restrict anonymous` in SMB, `no_root_squash` in NFS
- **SNMP community strings**: use strong strings instead of default `public`/`private`, prefer SNMPv3
- **Logging and alerting**: repeated 401/403 requests, many different usernames tried in a short window (spraying signature)

---
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > Enumeration_and_Brute_Force

---