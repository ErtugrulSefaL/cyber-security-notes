---
name: notes-admin
description: Maintain the cybersecurity notes vault — audit consistency, create new notes and MOCs following the vault metadata standard, and verify links/spelling before committing.
---

# Notes Admin — Cybersecurity Vault

This skill maintains the Obsidian cybersecurity notes vault in this repo. Follow the vault
conventions in `@.goosehints` and the index in `@README.md`.

## Tasks

### 1. audit
Check the whole vault for consistency issues before committing.

1. Verify every note/MOC has a `<details>` metadata block directly under the title.
2. Confirm no YAML frontmatter (`---` at the top of any file).
3. Check for broken relative Markdown links.
4. Confirm MOC files follow `0_<Topic>_MOC.md` and folder/file names are snake_case.
5. Ensure all content is English (except proper names) and dates are `YYYY-MM-DD`.
6. **MOC completeness**: for every folder that has an MOC, confirm the MOC lists ALL notes and sub-MOCs in that folder, and that it contains NO template placeholders (e.g. `note-1.md`, `tool-1.md`, `Room Name`). Flag any missing entry or stale placeholder.
7. Report findings as a checklist; do not fix without approval.

Useful commands:
```bash
# Check the repo is clean
git status --short

# Find any file starting with YAML frontmatter delimiter
git grep -l '^---$'

# List all Markdown files and their line counts
find . -name '*.md' -not -path './.git/*' | xargs wc -l

# List all MOCs
find . -name '0_*_MOC.md' -not -path './.git/*' | sort

# Compare notes vs MOC for a given folder:
# 1) notes present  find <dir> -maxdepth 1 -name '*.md' ! -name '0_*_MOC.md'
# 2) notes listed   grep -oE '\[[^]]+\]\([^)]+\)' <dir>/0_*_MOC.md
# Any note in (1) not in (2) is missing from the MOC.
```

### 2. new-note
Create a new note in the correct domain/subfolder.

1. Determine the topic domain from the HTB 3-domain architecture in `.goosehints`.
2. Use the latest naming convention, e.g. `snake_case_name.md`.
3. Generate the note body by copying `Templates/note_template.md`, filling the `<details>` metadata block (Main Topic = folder path, ISO date, status/wip).
4. After the metadata block, replace the `## Contents` placeholder with the note's guiding questions as anchor links (question-based outline). Anchor rule: lowercase the heading, spaces become `-`, drop punctuation like `?` (e.g. `What are their Benefits?` → `#what-are-their-benefits`).
5. Add the new note link to its domain MOC (`0_<Topic>_MOC.md`).
6. Verify the MOC is complete per the `audit` MOC completeness check (all sibling notes still listed, no placeholders).
7. Confirm with the user, then commit.

### room-writeup
Create a THM/mini-challenge writeup using `Templates/room_writeup_template.md`.

1. Place the file in the domain/subfolder matching the room topic.
2. Copy the template and fill the Writeup Metadata: Platform, Room, Task, Difficulty, Date (ISO), tags `#room #challenge`, status/wip.
3. Fill the guided-challenge sections: Connection, Recon & Enumeration, per-Question steps, Key Takeaways.
4. Link the writeup from the relevant domain MOC (add/replace its row in the `## Platform Writeups` table).
5. Verify the MOC is complete per the `audit` MOC completeness check.
6. Confirm with the user, then commit.

### 3. moc-update
Add or update an index MOC so it reflects the actual structure of its folder.

1. Use `0_<Topic>_MOC.md` naming in the relevant domain folder.
2. List ALL atomic notes in that folder (maxdepth 1) as relative links, grouped under the folder's natural sub-headings (e.g. `### Core Concepts & Sub-topics`, `### Tools`, `### Techniques`). For writeups, add a `## Platform Writeups` table.
3. List all sub-folder MOCs in the folder as relative links.
4. REMOVE any template placeholder entries (`note-1.md`, `tool-1.md`, `technique-1.md`, `Room Name` placeholder rows) — they are stale and misleading.
5. Ensure MOC metadata has `Type: Map of Content`, `Status: status/active`, and `#MOC #<topic>` tags.
6. Cross-link related MOCs where appropriate.
7. Verify the result against the audit `MOC completeness` check before committing.

### 4. verify
Confirmation step after edits.

1. Re-run the audit checks from the `audit` task.
2. Confirm zero broken links and zero missing metadata blocks.
3. Before commit, run `git status --short` to review what changed.
4. Only commit after user approval.

## General rules
- All new/reviewed content is English.
- Use `YYYY-MM-DD` dates.
- Make changes only with user approval; never commit unapproved changes.
- After important changes, suggest committing so work can be rolled back safely.
- If the vault structure or conventions change, suggest updating `.goosehints` or creating a recipe.