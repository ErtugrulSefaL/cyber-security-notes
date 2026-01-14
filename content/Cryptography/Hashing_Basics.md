---
Main Topic: Cryptography
Sub Topic: Hashing_Basics
Links:
  - https://tryhackme.com/room/hashingbasics
Date: 05-11-2025
Related Notes:
Tags:
  - Cyber_Security
  - Cryptography
  - Basics
  - THM
---

## Hashing_Basics


> [!INFO] Hashing
> **Hashing** is the process of converting input data (of any size) into a fixed-size value (called a _hash_ or _digest_) using a **hash function**.

- Hash functions are different from encryption. There is no key, and it’s meant to be impossible (or computationally impractical) to go from the output back to the input.
- Hashing helps protect data’s integrity and ensure password confidentiality.

---

### What’s a Hash Collision?

- A hash collision is when two different inputs give the same output.
- As there are more inputs than outputs, some inputs must inevitably give the same output.

> [!fail] Collision
>  MD5 and SHA1 have been attacked and are now considered insecure due to the ability to engineer hash collisions.
>  MD5 -> [MD5 Collision Demo](https://www.mscs.dal.ca/~selinger/md5collision/)
>  SHA1 -> [Shattered](https://shattered.io/)

---

### Password Cracking Tools

| Online Rainbow Tables | [CrackStation](https://crackstation.net/) [Hashes.com](https://hashes.com/en/decrypt/hash)                           |
| --------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Online Tools          | [Hashcat](https://hashcat.net/hashcat/) [John the Ripper](https://www.openwall.com/john/) [[John_the_Ripper___Basics]] |
