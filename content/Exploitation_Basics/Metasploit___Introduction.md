---
Main Topic: Cryptography
Sub Topic: Metasploit___Introduction
Links:
  - https://tryhackme.com/room/metasploitintro
Date: 17-11-2025
Related Notes:
Tags:
  - Cyber_Security
  - THM
  - Tools
  - Exploitation
---

# Metasploit___Introduction

> [!INFO] Metasploit
> Metasploit is the most widely used exploitation framework. Metasploit is a powerful tool that can support all phases of a penetration testing engagement, from information gathering to post-exploitation.


The main components of the Metasploit Framework can be summarized as follows;

- **msfconsole**: The main command-line interface.
- **Modules**: supporting modules such as exploits, scanners, payloads, etc.
- **Tools**: Stand-alone tools that will help vulnerability research, vulnerability assessment, or penetration testing. Some of these tools are msfvenom, pattern_create and pattern_offset. 

---
## Main components of Metasploit

Before diving into modules, it would be helpful to clarify a few recurring concepts: vulnerability, exploit, and payload.

- **Exploit:** A piece of code that uses a vulnerability present on the target system.
- **Vulnerability:** A design, coding, or logic flaw affecting the target system. The exploitation of a vulnerability can result in disclosing confidential information or allowing the attacker to execute code on the target system.
- **Payload:** An exploit will take advantage of a vulnerability. However, if we want the exploit to have the result we want (gaining access to the target system, read confidential information, etc.), we need to use a payload. Payloads are the code that will run on the target system.


Modules and categories under each one are listed below.

- **Auxiliary** - Any supporting module, such as scanners, crawlers and fuzzers, can be found here.

- **Encoders** - Encoders will allow you to encode the exploit and payload in the hope that a signature-based antivirus solution may miss them.

- **Evasion** - While encoders will encode the payload, they should not be considered a direct attempt to evade antivirus software. On the other hand, “evasion” modules will try that, with more or less success.

- **Exploits** - Exploits, neatly organized by target system.

- **NOPs** - NOPs (No OPeration) do nothing, literally. They are represented in the Intel x86 CPU family with 0x90, following which the CPU will do nothing for one cycle. They are often used as a buffer to achieve consistent payload sizes.

- **Payloads** - Payloads are codes that will run on the target system.
	You will see four different directories under payloads: adapters, singles, stagers and stages.
	- **Adapters:** An adapter wraps single payloads to convert them into different formats. For example, a normal single payload can be wrapped inside a Powershell adapter, which will make a single powershell command that will execute the payload.  
    
	- **Singles:** Self-contained payloads (add user, launch notepad.exe, etc.) that do not need to download an additional component to run.

	- **Stagers:** Responsible for setting up a connection channel between Metasploit and the target system. Useful when working with staged payloads. “Staged payloads” will first upload a stager on the target system then download the rest of the payload (stage). This provides some advantages as the initial size of the payload will be relatively small compared to the full payload sent at once.
	
	- **Stages:** Downloaded by the stager. This will allow you to use larger sized payloads.

- **Post** - Post modules will be useful on the final stage of the penetration testing process listed above, post-exploitation.