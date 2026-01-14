---
Main Topic: Cryptography
Sub Topic: Identifying_Hashes
Links:
  - https://tryhackme.com/room/johntheripperbasics
Date: 11-11-2025
Related Notes:
  - "[[John_the_Ripper___Basics]]"
  - "[[Hashing_Basics]]"
Tags:
  - Cryptography
  - Cyber_Security
  - THM
  - Tools
---

# Identifying_Hashes

## Identifying Hashes

Sometimes, John won’t play nicely with automatically recognising and loading hashes, but that’s okay! We can use other tools to identify the hash and then set John to a specific format. There are multiple ways to do this, such as using an online hash identifier like [this site](https://hashes.com/en/tools/hash_identifier). I like to use a tool called [hash-identifier](https://gitlab.com/kalilinux/packages/hash-identifier/-/tree/kali/master), a Python tool that is super easy to use and will tell you what different types of hashes the one you enter is likely to be, giving you more options if the first one fails.

To use hash-identifier, you can use `wget` or `curl` to download the Python file `hash-id.py` from its GitLab [page](https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py). Then, launch it with `python3 hash-id.py` and enter the hash you’re trying to identify. It will give you a list of the most probable formats. These two steps are shown in the terminal below.

Terminal

```
user@TryHackMe$ wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
$ python3 hash-id.py
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ \`\        #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__\`\   / ,__\ \ \  _ \`\      \ \ \   \ \ \ \ \     #
   #      \ \ \ \ \/\ \_\ \_/\__, \`\ \ \ \ \ \      \_\ \__ \ \ \_\ \     #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: 2e728dd31fb5949bc39cac5a9f066498

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))
```
