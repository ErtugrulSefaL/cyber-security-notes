---
Main Topic: Network_Security
Sub Topic: Net_Sec_Challenge
Links:
  - https://tryhackme.com/room/netsecchallenge
Date: 03-04-2026
Related Notes:
Tags:
---

# Net_Sec_Challenge

Target IP : 10.113.171.237
My IP : 192.168.173.115

```bash
telnet 10.113.171.237 80                        
Trying 10.113.171.237...  
Connected to 10.113.171.237.  
Escape character is '^]'.  
GET / HTTP/1.1  
Host: telnet  
  
HTTP/1.1 200 OK  
Vary: Accept-Encoding  
Content-Type: text/html  
Accept-Ranges: bytes  
ETag: "229449419"  
Last-Modified: Tue, 14 Sep 2021 07:33:09 GMT  
Content-Length: 226  
Date: Fri, 03 Apr 2026 17:34:42 GMT  
	Server: lighttpd THM{web_server_25352}  
  
<!DOCTYPE html>  
<html lang="en">  
<head>  
 <title>Hello, world!</title>  
 <meta charset="UTF-8" />  
 <meta name="viewport" content="width=device-width,initial-scale=1" />  
</head>  
<body>  
 <h1>Hello, world!</h1>  
</body>  
</html>  
Connection closed by foreign host.
```


```bash
telnet 10.113.171.237 22  
Trying 10.113.171.237...  
Connected to 10.113.171.237.  
Escape character is '^]'.  
SSH-2.0-OpenSSH_8.2p1 THM{946219583339}
```

```
[10021][ftp] host: 10.113.171.237   login: eddie   password: jordan
```

```
[10021][ftp] host: 10.113.171.237   login: quinn   password: andrea
```

New Target IP: 10.114.132.6


---

## Nmap testing
I was wondering how likely Nmap scans are to be detected and which ones are quieter. Luckily, they’ve included a challenge at the end of this room where we can test this. I’m going to try out most of the ones that come to mind and work my way from the loudest to the quietest.

| Command                         | % being detected |
| ------------------------------- | ---------------- |
| `nmap -sT 10.114.132.6 -p- -T5` | %100             |
| `nmap -sT 10.114.132.6 -p-`     | %100             |
| `nmap -sT 10.114.132.6`         | %100             |
| `nmap -sT 10.114.132.6 -F`      | %45              |
| `nmap -sS 10.114.132.6`         | %100             |
| `nmap -sS 10.114.132.6 -F`      | %45              |
| `nmap -sA 10.114.132.6`         | %100             |
| `nmap -sU 10.114.132.6`         | %5               |
| `nmap -sN 10.114.132.6`         | %0               |
| `nmap -sF 10.114.132.6`         | %100             |
| `nmap -sX 10.114.132.6`         | %100             |

