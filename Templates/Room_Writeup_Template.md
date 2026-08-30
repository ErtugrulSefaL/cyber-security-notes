**Main Topic:** <% tp.file.folder() %>
**Sub Topic:** <% tp.file.title %>
**Date:** <% tp.date.now("DD-MM-YYYY") %>
**Resources:**
**Related Notes:**
Tags: #status/wip 
<%*
const parts = tp.file.path(true).split("/").slice(0, -1);
const links = parts.map((part, i) => {
    const fullPath = parts.slice(0, i + 1).join("/");
    return `[${part}](/${fullPath}/0_${part}_MOC.md)`;
});
tR += links.join(" > ") + " > " + tp.file.title;
%>

---

# TryHackMe — <% tp.file.title %>

**Platform:** [TryHackMe](https://tryhackme.com)
**Room:**
**Task:**
**Difficulty:**

> **Note:** <!-- Delete this block if not a guided room. -->
> This is a guided TryHackMe room, not an independent CTF challenge.
> The target credentials were provided by the platform.
> The purpose of this writeup is to document the techniques practiced and
> reinforce learning, not to demonstrate blind exploitation.

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