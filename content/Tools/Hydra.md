---
Main Topic: Tools
Sub Topic: Hydra
Links:
  - https://example.com
  - https://example2.com
Date: 11-01-2026
Related Notes:
  - "[[Related Note 1]]"
  - "[[Related Note 2]]"
Tags:
  - topic
  - research
  - notes
---

# Hydra


> [!INFO] What's Hydra?
> Hydra is a brute force online password cracking program, a quick system login password “hacking” tool.

Hydra can run through a list and “brute force” some authentication services. Imagine trying to manually guess someone’s password on a particular service (SSH, Web Application Form, FTP or SNMP) - we can use Hydra to run through a password list and speed this process up for us, determining the correct password.

According to its [official repository](https://github.com/vanhauser-thc/thc-hydra), Hydra supports, i.e., has the ability to brute force the following protocols: “Asterisk, AFP, Cisco AAA, Cisco auth, Cisco enable, CVS, Firebird, FTP, HTTP\-FORM-GET, HTTP\-FORM-POST, HTTP\-GET, HTTP\-HEAD, HTTP\-POST, HTTP\-PROXY, HTTPS-FORM-GET, HTTPS-FORM-POST, HTTPS-GET, HTTPS-HEAD, HTTPS-POST, HTTP\-Proxy, ICQ, IMAP, IRC, LDAP, MEMCACHED, MONGODB, MS-SQL, MYSQL, NCP, NNTP, Oracle Listener, Oracle SID, Oracle, PC-Anywhere, PCNFS, POP3, POSTGRES, Radmin, RDP, Rexec, Rlogin, Rsh, RTSP, SAP/R3, SIP, SMB, SMTP, SMTP Enum, SNMP v1+v2+v3, SOCKS5, SSH (v1 and v2), SSHKEY, Subversion, TeamSpeak (TS2), Telnet, VMware-Auth, VNC and XMPP.”

## Using Hydra
### Hydra commands 
```
hydra -h          
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).  
  
Syntax: hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C FILE]] [-e nsr] [-o FILE] [-t TASKS] [-M FILE [-T TASKS]] [-w TIME] [-W TIME] [-f] [-s PORT] [-x MIN:MAX:CHARSET] [-c TIME] [-ISOuvVd46] [-m MODULE_OPT] [service://server[:POR  
T][/OPT]]  
  
Options:  
 -R        restore a previous aborted/crashed session  
 -I        ignore an existing restore file (don't wait 10 seconds)  
 -S        perform an SSL connect  
 -s PORT   if the service is on a different default port, define it here  
 -l LOGIN or -L FILE  login with LOGIN name, or load several logins from FILE  
 -p PASS  or -P FILE  try password PASS, or load several passwords from FILE  
 -x MIN:MAX:CHARSET  password bruteforce generation, type "-x -h" to get help  
 -y        disable use of symbols in bruteforce, see above  
 -r        use a non-random shuffling method for option -x  
 -e nsr    try "n" null password, "s" login as pass and/or "r" reversed login  
 -u        loop around users, not passwords (effective! implied with -x)  
 -C FILE   colon separated "login:pass" format, instead of -L/-P options  
 -M FILE   list of servers to attack, one entry per line, ':' to specify port  
 -D XofY   Divide wordlist into Y segments and use the Xth segment.  
 -o FILE   write found login/password pairs to FILE instead of stdout  
 -b FORMAT specify the format for the -o FILE: text(default), json, jsonv1  
 -f / -F   exit when a login/pass pair is found (-M: -f per host, -F global)  
 -t TASKS  run TASKS number of connects in parallel per target (default: 16)  
 -T TASKS  run TASKS connects in parallel overall (for -M, default: 64)  
 -w / -W TIME  wait time for a response (32) / between connects per thread (0)  
 -c TIME   wait time per login attempt over all threads (enforces -t 1)  
 -4 / -6   use IPv4 (default) / IPv6 addresses (put always in [] also in -M)  
 -v / -V / -d  verbose mode / show login+pass for each attempt / debug mode    
 -O        use old SSL v2 and v3  
 -K        do not redo failed attempts (good for -M mass scanning)  
 -q        do not print messages about connection errors  
 -U        service module usage details  
 -m OPT    options specific for a module, see -U output for information  
 -h        more command line options (COMPLETE HELP)  
 server    the target: DNS, IP or 192.168.0.0/24 (this OR the -M option)  
 service   the service to crack (see below for supported protocols)  
 OPT       some service modules support additional input (-U for module help)  
  
Supported services: adam6500 asterisk cisco cisco-enable cobaltstrike cvs firebird ftp[s] http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] memcached mong  
odb mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smb2 smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp  
  
Hydra is a tool to guess/crack valid login/password pairs.  
Licensed under AGPL v3.0. The newest version is always available at;  
https://github.com/vanhauser-thc/thc-hydra  
Please don't use in military or secret service organizations, or for illegal  
purposes. (This is a wish and non-binding - most such people do not care about  
laws and ethics anyway - and tell themselves they are one of the good ones.)  
These services were not compiled in: afp ncp oracle sapr3.  
  
Use HYDRA_PROXY_HTTP or HYDRA_PROXY environment variables for a proxy setup.  
E.g. % export HYDRA_PROXY=socks5://l:p@127.0.0.1:9150 (or: socks4:// connect://)  
    % export HYDRA_PROXY=connect_and_socks_proxylist.txt  (up to 64 entries)  
    % export HYDRA_PROXY_HTTP=http://login:pass@proxy:8080  
    % export HYDRA_PROXY_HTTP=proxylist.txt  (up to 64 entries)  
  
Examples:  
 hydra -l user -P passlist.txt ftp://192.168.0.1  
 hydra -L userlist.txt -p defaultpw imap://192.168.0.1/PLAIN  
 hydra -C defaults.txt -6 pop3s://[2001:db8::1]:143/TLS:DIGEST-MD5  
 hydra -l admin -p password ftp://[192.168.0.0/24]/  
 hydra -L logins.txt -P pws.txt -M targets.txt ssh
```

Also we can use [hydra's man page](https://man.archlinux.org/man/hydra.1.en)or `man hydra`.
For more information on the options of each protocol in Hydra, you can check the [Kali Hydra tool page](https://en.kali.tools/?p=220).

### [THM Example Usage](https://tryhackme.com/room/hydra?taskNo=2&sharerId=6702abe0f77a70c90629f7db)

The options we pass into Hydra depend on which service (protocol) we’re attacking. For example, if we wanted to brute force FTP with the username being `user` and a password list being `passlist.txt`, we’d use the following command:

`hydra -l user -P passlist.txt ftp://MACHINE_IP`

For this deployed machine, here are the commands to use Hydra on SSH and a web form (POST method).

#### SSH

`hydra -l <username> -P <full path to pass> MACHINE_IP -t 4 ssh`

| Option | Description |
| --- | --- |
| `-l` | specifies the (SSH) username for login |
| `-P` | indicates a list of passwords |
| `-t` | sets the number of threads to spawn |

For example, `hydra -l root -P passwords.txt MACHINE_IP -t 4 ssh` will run with the following arguments:

- Hydra will use `root` as the username for `ssh`
- It will try the passwords in the `passwords.txt` file
- There will be four threads running in parallel as indicated by `-t 4`

#### Post Web Form

We can use Hydra to brute force web forms too. You must know which type of request it is making; GET or POST methods are commonly used. You can use your browser’s network tab (in developer tools) to see the request types or view the source code.

`sudo hydra <username> <wordlist> MACHINE_IP http-post-form "<path>:<login_credentials>:<invalid_response>"`

| Option | Description |
| --- | --- |
| `-l` | the username for (web form) login |
| `-P` | the password list to use |
| `http-post-form` | the type of the form is POST |
| `<path>` | the login page URL, for example, `login.php` |
| `<login_credentials>` | the username and password used to log in, for example, `username=^USER^&password=^PASS^` |
| `<invalid_response>` | part of the response when the login fails |
| `-V` | verbose output for every attempt |

Below is a more concrete example Hydra command to brute force a POST login form:

`hydra -l <username> -P <wordlist> MACHINE_IP http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V`

- The login page is only `/`, i.e., the main IP address.
- The `username` is the form field where the username is entered
- The specified username(s) will replace `^USER^`
- The `password` is the form field where the password is entered
- The provided passwords will be replacing `^PASS^`
- Finally, `F=incorrect` is a string that appears in the server reply when the login fails