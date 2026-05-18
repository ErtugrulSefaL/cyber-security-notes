---
Main Topic: Network_Security
Sub Topic: Protocols_and_Servers_1
Links:
  - https://tryhackme.com/room/protocolsandservers
Date: 01-04-2026
Related Notes:
Tags:
---

# Protocols_and_Servers_1

```bash
telnet 10.114.134.244 80  
Trying 10.114.134.244...  
Connected to 10.114.134.244.  
Escape character is '^]'.  
GET /flag.thm HTTP/1.1  
Host: 10.114.134.244  
  
HTTP/1.1 200 OK  
Server: nginx/1.18.0 (Ubuntu)  
Date: Wed, 01 Apr 2026 20:02:45 GMT  
Content-Type: application/octet-stream  
Content-Length: 39  
Last-Modified: Wed, 15 Sep 2021 09:19:23 GMT  
Connection: keep-alive  
ETag: "6141ba9b-27"  
Accept-Ranges: bytes  
  
THM{e3eb0a1df437f3f97a64aca5952c8ea0}
```

This room covered various protocols, their usage, and how they work under the hood. Many other standard protocols are of interest to attackers. For instance, Server Message Block (SMB) provides shared access to files and printers between networks, and it can be an exciting target. However, this room intends only to give you a good knowledge of a few common protocols and how they work under the hood. One room or even a complete module can’t cover all the network protocols.

It is good to remember the default port number for common protocols. Below is a summary of the protocols we covered, sorted in alphabetical order, along with their default port numbers.

| Protocol | Port | Application(s) | Data Security |
| -------- | ---- | -------------- | ------------- |
| FTP      | 21   | File Transfer  | Cleartext     |
| HTTP     | 80   | Worldwide Web  | Cleartext     |
| IMAP     | 143  | Email (MDA)    | Cleartext     |
| POP3     | 110  | Email (MDA)    | Cleartext     |
| SMTP     | 25   | Email (MTA)    | Cleartext     |
| Telnet   | 23   | Remote Access  | Cleartext     |