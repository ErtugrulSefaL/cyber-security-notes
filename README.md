# Cybersecurity Notes

- My **cybersecurity notes and resources** built while studying on [TryHackMe](https://tryhackme.com) and related platforms.
- The goal is to create a **clear, structured, and practical knowledge base** that can be useful both for myself and for others learning cybersecurity.

> [!WARNING]
> This content is based on my ongoing personal learning journey and focuses on **fundamental concepts, tools, defense techniques, and resources**. I am still learning, so if you come across any incorrect information, please let me know, or if you have any advice along those lines, I would appreciate it if you could share it with me. This way, I can continue to improve myself🙂.

---

## 📚 MAIN TOPICS

The notes are organized following the HTB Academy 3-domain model: **Offensive Security**, **Defensive Security**, and **General** fundamentals. Tools and templates live as cross-cutting top-level folders.

| Topic                                                                                      | Description                              |
| ------------------------------------------------------------------------------------------ | ---------------------------------------- |
| [General — Cryptography](/General/Cryptography/0_Cryptography_MOC.md)                      | Hashing, encoding, encryption, cracking  |
| [General — Networking](/General/Networking/0_Networking_MOC.md)                            | Protocols, servers, baseline networking  |
| [Defensive Security](/Defensive_Security/0_Defensive_Security_MOC.md)                      | SOC, forensics, incident response        |
| [Offensive Security](/Offensive_Security/0_Offensive_Security_MOC.md)                      | Offensive methodology, exploits, privesc |
| &nbsp;&nbsp;· [Reconnaissance](/Offensive_Security/Reconnaissance/0_Reconnaissance_MOC.md) | Nmap, active/passive recon, OSINT        |
| &nbsp;&nbsp;· [Web Pentesting](/Offensive_Security/Web_Pentesting/0_Web_Pentesting_MOC.md) | OWASP, API security, web attacks         |
| [Tools](/Tools/0_Tools_MOC.md)                                                             | Tool references and cheatsheets          |

---

## Structure

```
CYBER_SECURITY/
├── General/                        # cross-cutting fundamentals
│   ├── Cryptography/
│   └── Networking/
├── Defensive_Security/             # SOC, forensics, IR
├── Offensive_Security/             # methodology & exploits
│   ├── Reconnaissance/
│   ├── Enumeration_and_Brute_Force.md
│   ├── shell_notes.md
│   ├── Penetration_Testing_Foundations/
│   ├── Vulnerability_Knowledge/
│   ├── Linux_Privilege_Escalation/
│   └── Web_Pentesting/             # OWASP, API, web attacks
└── Tools/                          # Cross-cutting tool references & cheatsheets
```

Each domain folder contains an index MOC (`0_<Topic>_MOC.md`), atomic concept notes, and subfolders for topics/tools with multiple notes.

---

## Platforms

| Platform | Type |
| --- | --- |
| [TryHackMe](https://tryhackme.com) | Guided rooms, CTF-style labs, learning paths |

---

## Status Tags

| Tag | Meaning |
| --- | --- |
| `status/wip` | Work in progress |
| `status/done` | Complete and reviewed |
| `status/review` | Needs revisiting |
| `status/active` | MOCs: actively maintained index |