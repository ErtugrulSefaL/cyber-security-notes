<%*
const parts = tp.file.path(true).split("/").slice(0, -1);
const links = parts.map((part, i) => {
    const fullPath = parts.slice(0, i + 1).join("/");
    return `[${part}](/${fullPath}/0_${part}_MOC.md)`;
});
tR += links.join(" > ") + " > " + tp.file.title;
%>

# <% tp.file.title %> — Room Mini-Challenge

<details open>
<summary>📋 Writeup Metadata</summary>

<dl>
<dt><b>Platform</b></dt><dd>[TryHackMe](https://tryhackme.com)</dd>
<dt><b>Room</b></dt><dd></dd>
<dt><b>Task</b></dt><dd></dd>
<dt><b>Difficulty</b></dt><dd></dd>
<dt><b>Date</b></dt><dd><% tp.date.now("YYYY-MM-DD") %></dd>
<dt><b>Tags</b></dt><dd>#room #challenge</dd>
<dt><b>Status</b></dt><dd>status/wip</dd>
</dl>

</details>

> **Note:** This is the guided challenge / practice section at the end of a room.
> It is meant to reinforce the techniques covered in the room, not to demonstrate
> blind exploitation. <!-- Delete this block if not a guided practice section. -->

---

## Connection

```bash
ssh user@<TARGET_IP>
# Password:
```

```bash
# Shell stabilisation (if needed)
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

---

## Recon & Enumeration

```bash
nmap -sC -sV -oN nmap.txt <TARGET_IP>
```

```
# Paste nmap output here
```

<!-- Add subsections per discovered service if needed -->

---

## Question 1 —

<!-- Describe what the question asks, what you did to answer it, and why it worked -->

```bash

```

---

## Question 2 —

```bash

```

---

## Question 3 —

```bash

```

---

## Key Takeaways

<!-- Write these as principles, not just "I did X". What would you tell someone who hasn't done this room? -->
- 
- 

---

## Related Notes
- [Topic Name](../../Domain/note-name.md)

---
<%*
const parts1 = tp.file.path(true).split("/").slice(0, -1);
const links1 = parts1.map((part, i) => {
    const fullPath1 = parts1.slice(0, i + 1).join("/");
    return `[${part}](/${fullPath1}/0_${part}_MOC.md)`;
});
tR += links1.join(" > ") + " > " + tp.file.title;
%>