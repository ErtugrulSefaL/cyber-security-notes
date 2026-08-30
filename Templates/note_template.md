<%*
const parts = tp.file.path(true).split("/").slice(0, -1);
const links = parts.map((part, i) => {
    const fullPath = parts.slice(0, i + 1).join("/");
    return `[${part}](/${fullPath}/0_${part}_MOC.md)`;
});
tR += links.join(" > ") + " > " + tp.file.title;
%>

# <% tp.file.title %>

> [!NOTE] 📋 Note Metadata — Details
> <details>
> <summary>🖊️ Expand</summary>
>
> <dl>
> <dt><b>Main Topic</b></dt><dd><% tp.file.folder() %></dd>
> <dt><b>Date</b></dt><dd><% tp.date.now("YYYY-MM-DD") %></dd>
> <dt><b>Resources</b></dt><dd></dd>
> <dt><b>Related Notes</b></dt><dd></dd>
> <dt><b>Status</b></dt><dd><code>status/wip</code></dd>
> </dl>
> </details>

---

Write notes here...

---
<%*
const parts2 = tp.file.path(true).split("/").slice(0, -1);
const links2 = parts2.map((part, i) => {
    const fullPath = parts2.slice(0, i + 1).join("/");
    return `[${part}](/${fullPath}/0_${part}_MOC.md)`;
});
tR += links2.join(" > ") + " > " + tp.file.title;
%>