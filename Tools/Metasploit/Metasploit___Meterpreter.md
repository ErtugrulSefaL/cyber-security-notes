---
Main Topic: Exploitation_Basics
Sub Topic: Metasploit___Meterpreter
Links:
  - https://example.com
  - https://example2.com
Date: 08-01-2026
Related Notes:
  - "[[Related Note 1]]"
  - "[[Related Note 2]]"
Tags:
  - topic
  - research
  - notes
---

# Metasploit___Meterpreter

## How does Meterpreter work?

Meterpreter runs on the target system but is not installed on it. It runs in memory and does not write itself to the disk on the target. This feature aims to avoid being detected during antivirus scans. By default, most antivirus software will scan new files on the disk (e.g. when you download a file from the internet) Meterpreter runs in memory (RAM - Random Access Memory) to avoid having a file that has to be written to the disk on the target system (e.g. meterpreter.exe). This way, Meterpreter will be seen as a process and not have a file on the target system.

Meterpreter also aims to avoid being detected by network-based IPS (Intrusion Prevention System) and IDS (Intrusion Detection System) solutions by using encrypted communication with the server where Metasploit runs (typically your attacking machine). If the target organization does not decrypt and inspect encrypted traffic (e.g. HTTPS) coming to and going out of the local network, IPS and IDS solutions will not be able to detect its activities.  

While Meterpreter is recognized by major antivirus software, this feature provides some degree of stealth.


## Meterpreter's Great Benefits

**Meterpreter has a wide range of different versions you can choose from based on your target system.**

The easiest way to have an idea about available Meterpreter versions could be to list them using msfvenom, as seen below. 

We have used the `msfvenom --list payloads` command and grepped "meterpreter" payloads (adding `| grep meterpreter` to the command line).

![[Pasted image 20260108014309.png]]
I get image from [TryHackMe](https://tryhackme.com/room/meterpreter) because my `msfvenom --list payloads | grep meterpreter` output is very long.


> [!tip] WARNING!!!
> The list will show Meterpreter versions available for the following platforms;
> 
> - Android
> - Apple iOS
> - Java
> - Linux
> - OSX
> - PHP
> - Python
> - Windows
> 
Your decision on which version of Meterpreter to use will be mostly based on three factors;
> 
> - The target operating system (Is the target operating system Linux or Windows? Is it a Mac device? Is it an Android phone? etc.)
> - Components available on the target system (Is Python installed? Is this a PHP website? etc.)
> - Network connection types you can have with the target system (Do they allow raw TCP connections? Can you only have an HTTPS reverse connection? Are IPv6 addresses not as closely monitored as IPv4 addresses? etc.)

## Meterpreter Commamds

```
meterpreter > help  
  
Core Commands  
=============  
  
   Command                   Description  
   -------                   -----------  
   ?                         Help menu  
   background                Backgrounds the current session  
   bg                        Alias for background  
   bgkill                    Kills a background meterpreter script  
   bglist                    Lists running background scripts  
   bgrun                     Executes a meterpreter script as a background thread  
   channel                   Displays information or control active channels  
   close                     Closes a channel  
   detach                    Detach the meterpreter session (for http/https)  
   disable_unicode_encoding  Disables encoding of unicode strings  
   enable_unicode_encoding   Enables encoding of unicode strings  
   exit                      Terminate the meterpreter session  
   get_timeouts              Get the current session timeout values  
   guid                      Get the session GUID  
   help                      Help menu  
   info                      Displays information about a Post module  
   irb                       Open an interactive Ruby shell on the current session  
   load                      Load one or more meterpreter extensions  
   machine_id                Get the MSF ID of the machine attached to the session  
   migrate                   Migrate the server to another process  
   pivot                     Manage pivot listeners  
   pry                       Open the Pry debugger on the current session  
   quit                      Terminate the meterpreter session  
   read                      Reads data from a channel  
   resource                  Run the commands stored in a file  
   run                       Executes a meterpreter script or Post module  
   secure                    (Re)Negotiate TLV packet encryption on the session  
   sessions                  Quickly switch to another session  
   set_timeouts              Set the current session timeout values  
   sleep                     Force Meterpreter to go quiet, then re-establish session  
   ssl_verify                Modify the SSL certificate verification setting  
   transport                 Manage the transport mechanisms  
   use                       Deprecated alias for "load"  
   uuid                      Get the UUID for the current session  
   write                     Writes data to a channel  
  
  
Priv: Elevate Commands  
======================  
  
   Command                   Description  
   -------                   -----------  
   getsystem                 Attempt to elevate your privilege to that of local system.  
  
  
Priv: Password database Commands  
================================  
  
   Command                   Description  
   -------                   -----------  
   hashdump                  Dumps the contents of the SAM database  
  
  
Priv: Timestomp Commands  
========================  
  
   Command                   Description  
   -------                   -----------  
   timestomp                 Manipulate file MACE attributes  
  
  
Stdapi: File system Commands  
============================  
  
   Command                   Description  
   -------                   -----------  
   cat                       Read the contents of a file to the screen  
   cd                        Change directory  
   checksum                  Retrieve the checksum of a file  
   cp                        Copy source to destination  
   del                       Delete the specified file  
   dir                       List files (alias for ls)  
   download                  Download a file or directory  
   edit                      Edit a file  
   getlwd                    Print local working directory (alias for lpwd)  
   getwd                     Print working directory  
   lcat                      Read the contents of a local file to the screen  
   lcd                       Change local working directory  
   ldir                      List local files (alias for lls)  
   lls                       List local files  
   lmkdir                    Create new directory on local machine  
   lpwd                      Print local working directory  
   ls                        List files  
   mkdir                     Make directory  
   mv                        Move source to destination  
   pwd                       Print working directory  
   rm                        Delete the specified file  
   rmdir                     Remove directory  
   search                    Search for files  
   show_mount                List all mount points/logical drives  
   upload                    Upload a file or directory  
  
  
Stdapi: Networking Commands  
===========================  
  
   Command                   Description  
   -------                   -----------  
   arp                       Display the host ARP cache  
   getproxy                  Display the current proxy configuration  
   ifconfig                  Display interfaces  
   ipconfig                  Display interfaces  
   netstat                   Display the network connections  
   portfwd                   Forward a local port to a remote service  
   resolve                   Resolve a set of host names on the target  
   route                     View and modify the routing table  
  
  
Stdapi: System Commands  
=======================  
  
   Command                   Description  
   -------                   -----------  
   clearev                   Clear the event log  
   drop_token                Relinquishes any active impersonation token.  
   execute                   Execute a command  
   getenv                    Get one or more environment variable values  
   getpid                    Get the current process identifier  
   getprivs                  Attempt to enable all privileges available to the current process  
   getsid                    Get the SID of the user that the server is running as  
   getuid                    Get the user that the server is running as  
   kill                      Terminate a process  
   localtime                 Displays the target system local date and time  
   pgrep                     Filter processes by name  
   pkill                     Terminate processes by name  
   ps                        List running processes  
   reboot                    Reboots the remote computer  
   reg                       Modify and interact with the remote registry  
   rev2self                  Calls RevertToSelf() on the remote machine  
   shell                     Drop into a system command shell  
   shutdown                  Shuts down the remote computer  
   steal_token               Attempts to steal an impersonation token from the target process  
   suspend                   Suspends or resumes a list of processes  
   sysinfo                   Gets information about the remote system, such as OS  
  
  
Stdapi: User interface Commands  
===============================  
  
   Command                   Description  
   -------                   -----------  
   enumdesktops              List all accessible desktops and window stations  
   getdesktop                Get the current meterpreter desktop  
   idletime                  Returns the number of seconds the remote user has been idle  
   keyboard_send             Send keystrokes  
   keyevent                  Send key events  
   keyscan_dump              Dump the keystroke buffer  
   keyscan_start             Start capturing keystrokes  
   keyscan_stop              Stop capturing keystrokes  
   mouse                     Send mouse events  
   screenshare               Watch the remote user desktop in real time  
   screenshot                Grab a screenshot of the interactive desktop  
   setdesktop                Change the meterpreters current desktop  
   uictl                     Control some of the user interface components  
  
  
Stdapi: Webcam Commands  
=======================  
  
   Command                   Description  
   -------                   -----------  
   record_mic                Record audio from the default microphone for X seconds  
   webcam_chat               Start a video chat  
   webcam_list               List webcams  
   webcam_snap               Take a snapshot from the specified webcam  
   webcam_stream             Play a video stream from the specified webcam  
  
  
Stdapi: Audio Output Commands  
=============================  
  
   Command                   Description  
   -------                   -----------  
   play                      play a waveform audio file (.wav) on the target system
```

(Also this is a good website -> [Meterpreter-basics](https://www.offsec.com/metasploit-unleashed/meterpreter-basics/))
