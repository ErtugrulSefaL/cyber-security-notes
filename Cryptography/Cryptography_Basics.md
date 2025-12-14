---
Main Topic: Cryptography
Sub Topic: Cryptography_Basics
Links:
  - https://tryhackme.com/room/cryptographybasics
Date: 04-11-2025
Related Notes:
Tags:
  - THM
  - Cyber_Security
  - Cryptography
  - Basics
---

## Cryptography_Basics

The two main categories of encryption are **symmetric** and **asymmetric**.

### Symmetric Encryption

* Uses the same key to encrypt and decrypt the data
* Keeping the key secret is a must; it is also called **private key cryptography**.
* Examples of symmetric encryption are DES (Data Encryption Standard), 3DES (Triple DES) and AES (Advanced Encryption Standard).
	* **DES** was adopted as a standard in 1977 and uses a 56-bit key. With the advancement in computing power, in 1999, a DES key was successfully broken in less than 24 hours, motivating the shift to 3DES.
	* **3DES** is DES applied three times; consequently, the key size is 168 bits, though the effective security is 112 bits. 3DES was more of an ad-hoc solution when DES was no longer considered secure. 3DES was deprecated in 2019 and should be replaced by AES; however, it may still be found in some legacy systems.
	* **AES** was adopted as a standard in 2001. Its key size can be 128, 192, or 256 bits.


### Asymmetric Encryption

- Uses a pair of keys, one to encrypt and the other to decrypt
- Examples are RSA, Diffie-Hellman, and Elliptic Curve cryptography (ECC). The two keys involved in the process are referred to as a **public key** and a **private key**. Data encrypted with the public key can be decrypted with the private key. Your private key needs to be kept private, hence the name.
- Asymmetric encryption tends to be slower, and many asymmetric encryption ciphers use larger keys than symmetric encryption. For example, RSA uses 2048-bit, 3072-bit, and 4096-bit keys; 2048-bit is the recommended minimum key size. Diffie-Hellman also has a recommended minimum key size of 2048 bits but uses 3072-bit and 4096-bit keys for enhanced security. On the other hand, ECC can achieve equivalent security with shorter keys. For example, with a 256-bit key, ECC provides a level of security comparable to a 3072-bit RSA key.

