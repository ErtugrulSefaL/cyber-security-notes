---
Main Topic: Network
Sub Topic: NSE_Nmap_Scripting_Engine
Links:
  - https://tryhackme.com/room/furthernmap?taskNo=10&sharerId=6702abe0f77a70c90629f7db
Date: 09-01-2026
Related Notes:
  - "[[Nmap]]"
Tags:
  - Tools
---

# NSE_Nmap_Scripting_Engine

## What's the NSE

The **N**map **S**cripting **E**ngine (NSE) is an incredibly powerful addition to Nmap, extending its functionality quite considerably. NSE Scripts are written in the *Lua* programming language, and can be used to do a variety of things: from scanning for vulnerabilities, to automating exploits for them. The NSE is particularly useful for reconnaisance, however, it is well worth bearing in mind how extensive the script library is.

There are many categories available. Some useful categories include:

- `safe`:- Won't affect the target
- `intrusive`:- Not safe: likely to affect the target
- `vuln`:- Scan for vulnerabilities
- `exploit`:- Attempt to exploit a vulnerability
- `auth`:- Attempt to bypass authentication for running services (e.g. Log into an FTP server anonymously)
- `brute`:- Attempt to bruteforce credentials for running services
- `discovery`:- Attempt to query running services for further information about the network (e.g. query an SNMP server).

A more exhaustive list can be found [nmap website](https://nmap.org/book/nse-usage.html).

---
## Working with NSE

- To run this categories, we use ==`nmap --script=<category or script-name`==.

- Some scripts require arguments (for example, credentials, if they're exploiting an authenticated vulnerability). These can be given with the `--script-args` Nmap switch. An example of this would be with the `http-put` script (used to upload files using the PUT method). This takes two arguments: the URL to upload the file to, and the file's location on disk.  For example:

	`nmap -p 80 --script http-put --script-args http-put.url='/dav/shell.php',http-put.file='./shell.php'`

	Note that the arguments are separated by commas, and connected to the corresponding script with periods (i.e.  ==`nmap <script-name>.<argument>`==).

- Nmap scripts come with built-in help menus, which can be accessed using ==`nmap --script-help <script-name>`==. This tends not to be as extensive as in the link given above, however, it can still be useful when working locally.

---
## Searching for Scripts

We have two options for this, which should ideally be used in conjunction with each other. The first is the page on the [Nmap website](https://nmap.org/nsedoc/) (mentioned in the previous task) which contains a list of all official scripts. The second is the local storage on your attacking machine. Nmap stores its scripts on Linux at `/usr/share/nmap/scripts`. All of the NSE scripts are stored in this directory by default -- this is where Nmap looks for scripts when you specify them.

There are two ways to search for installed scripts. One is by using the `/usr/share/nmap/scripts/script.db` file. Despite the extension, this isn't actually a database so much as a formatted text file containing filenames and categories for each available script.

1. Using "grep" command
	Example: `grep "ftp" /usr/share/nmap/scripts/script.db`

2. Using "ls" command 
	Example: `ls -l /usr/share/nmap/scripts/*ftp*`

---

## Installing New Scripts

We mentioned previously that the Nmap website contains a list of scripts, so, what happens if one of these is missing in the `scripts` directory locally? A standard `sudo apt update && sudo apt install nmap` should fix this; however, it's also possible to install the scripts manually by downloading the script from Nmap (`sudo wget -O /usr/share/nmap/scripts/<script-name>.nse https://svn.nmap.org/nmap/scripts/<script-name>.nse`). This must then be followed up with `nmap --script-updatedb`, which updates the `script.db` file to contain the newly downloaded script.