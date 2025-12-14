---
Main Topic: Cryptography
Sub Topic: John_the_Ripper___Basics
Links:
  - https://tryhackme.com/room/johntheripperbasics
Date: 05-11-2025
Related Notes:
Tags:
  - Cyber_Security
  - Cryptography
  - Basics
  - THM
  - Tools
---

## John_the_Ripper___Basics

> [!INFO] John the Ripper
> John the Ripper is a well-known, well-loved, and versatile hash-cracking tool. It combines a fast cracking speed with an extraordinary range of compatible hash types.

- Even though the algorithm is not feasibly reversible, that doesn’t mean cracking the hashes is impossible. If you have the hashed version of a password, for example, and you know the hashing algorithm, you can use that hashing algorithm to hash a large number of words, called a dictionary. You can then compare these hashes to the one you’re trying to crack to see if they match. If they do, you know what word corresponds to that hash- you’ve cracked it!
- This process is called a ***dictionary attack****, and John the Ripper, or John as it’s commonly shortened, is a tool for conducting fast brute force attacks on various hash types.

---

### Wordlists

- To use a dictionary attack against hashes, you need a list of words to hash and compare; unsurprisingly, this is called a wordlist. There are many different wordlists out there, and a good collection can be found in the [SecLists](https://github.com/danielmiessler/SecLists) repository. There are a few places you can look for wordlists for attacking the system of choice; we will quickly run through where you can find them.

> [!INFO] Path
> `/usr/share/wordlists` directory contains a series of great wordlists.

> [!FAQ] RockYou
> For all of the tasks in this room, we will use the infamous `rockyou.txt` wordlist, a very large common password wordlist obtained from a data breach on a website called rockyou.com in 2009. If you are not using any of the above distributions, you can get the `rockyou.txt` wordlist from the [SecLists](https://github.com/danielmiessler/SecLists) repository under the `/Passwords/Leaked-Databases` subsection. You may need to extract it from the `.tar.gz` format using `tar xvzf rockyou.txt.tar.gz`.


### John Basic Syntax

> [!NOTE] **`john [options] [file path]`** 
> **\[options\]**
> - `--wordlist=[path to wordlist]`: Specifies using wordlist mode, reading from the file that you supply in the provided path
> - `--format=[format]`: This is the flag to tell John that you’re giving it a hash of a specific format and to use the following format to crack it
> 	- for example `john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt`
> - `--list=WHAT` : List capabilities - 
> 	- for example you can use it you can list all of John’s formats using `john --list=formats` and either check manually or grep for your hash type using something like `john --list=formats | grep -iF "md5"`
> 

**There are a lot of usage and options for john. These are the basics.**

---

### Authentication Hashes
Now that we understand the basic syntax and usage of John the Ripper, let’s move on to cracking something a little bit more complicated, something that you may even want to attempt if you’re on an actual Penetration Test or Red Team engagement. Authentication hashes are the hashed versions of passwords stored by operating systems; it is sometimes possible to crack them using our brute-force methods. To get your hands on these hashes, you must often already be a privileged user, so we will explain some of the hashes we plan on cracking as we attempt them.

#### Windows
##### NTHash / NTLM
NThash is the hash format modern Windows operating system machines use to store user and service passwords. It’s also commonly referred to as NTLM, which references the previous version of Windows format for hashing passwords known as LM, thus NT/LM.

A bit of history: the NT designation for Windows products originally meant New Technology. It was used starting with Windows NT to denote products not built from the MS-DOS Operating System. Eventually, the “NT” line became the standard Operating System type to be released by Microsoft, and the name was dropped, but it still lives on in the names of some Microsoft technologies.

In Windows, SAM (Security Account Manager) is used to store user account information, including usernames and hashed passwords. You can acquire NTHash/NTLM hashes by dumping the SAM database on a Windows machine, using a tool like [Mimikatz](https://github.com/ParrotSec/mimikatz), or using the Active Directory database: `NTDS.dit`. You may not have to crack the hash to continue privilege escalation, as you can often conduct a “pass the hash” attack instead, but sometimes, hash cracking is a viable option if there is a weak password policy.

> [!NOTE] for Windows hashes
> `--format=nt`


#### Linux
##### Cracking Hashes from /etc/shadow
The `/etc/shadow` file is the file on Linux machines where password hashes are stored. It also stores other information, such as the date of last password change and password expiration information. It contains one entry per line for each user or user account of the system. This file is usually only accessible by the root user, so you must have sufficient privileges to access the hashes. However, if you do, there is a chance that you will be able to crack some of the hashes.

John can be very particular about the formats it needs data in to be able to work with it; for this reason, to crack `/etc/shadow` passwords, you must combine it with the `/etc/passwd` file for John to understand the data it’s being given. To do this, we use a tool built into the John suite of tools called ==`unshadow`==. The basic syntax of `unshadow` is as follows:

`unshadow [path to passwd] [path to shadow]`

- `unshadow`: Invokes the unshadow tool
- `[path to passwd]`: The file that contains the copy of the `/etc/passwd` file you’ve taken from the target machine
- `[path to shadow]`: The file that contains the copy of the `/etc/shadow` file you’ve taken from the target machine

**Example Usage:**

`unshadow local_passwd local_shadow > unshadowed.txt`

Then we can crack unshadowed.txt by john.

---

### Conversion to John

John can be very particular about the formats it needs data in to be able to work with it; for this reason, we must convert the data for john similarly to `unshadow` tool.

- for zip files -> zip2john
	`zip2john zipfile.zip > zip_hash.txt`
- for rar files -> rar2john
	`/opt/john/rar2john rarfile.rar > rar_hash.txt`
- for ssh files -> ssh2john
	`/opt/john/ssh2john.py id_rsa > id_rsa_hash.txt`






