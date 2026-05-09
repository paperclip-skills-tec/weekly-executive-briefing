---
name: weekly-executive-briefing
description: >
  Canonical protocol for producing the Weekly Executive Briefing — a structured
  vault document that gives Matt a consistent, scannable summary of all active
  projects every week. Use this skill whenever the Weekly Executive Briefing
  routine fires, whenever an agent is asked for a weekly summary, executive
  briefing, project status roundup, or "how are all the projects going". Also
  use for any ad-hoc executive summary request even outside the scheduled
  routine. This skill defines the exact sections, data sources, output file
  location, RAG status rules, and cross-linking requirements — do not free-form
  the briefing format, even when the request looks simple. Consistency across
  weeks is the point.
---

## Purpose

This skill eliminates structural drift in the Weekly Executive Briefing. Every
run must produce a document that Matt can open, scan in two minutes, and act on
— without needing to remember what last week's version looked like. Consistency
in location, section order, and section depth is a first-class requirement.

---

## Step 1 — Compute the Week Reference

Determine the ISO week number for today's date: `YYYY-WNN` (e.g. `2026-W19`).
This is the file name anchor and appears in the document title.

Use Python or shell if needed:
```bash
python3 -c "import datetime; d=datetime.date.today(); print(f'{d.year}-W{d.isocalendar()[1]:02d}')"
```

---

## Step 2 — Gather Data Sources

Consult all of the following before drafting any section. Do not draft from
memory alone.

| Source | What to extract |
|--------|----------------|
| `01_Projects/` index files | Current status, owner, milestone, last update |
| `_Daily/` notes from the **past 7 days** | Decisions made, blockers surfaced, actions taken |
| Action register (wherever current) | Open actions, owners, due dates |
| Risk register (wherever current) | Open risks, ratings, mitigations |
| Paperclip issues in `in_progress` or `blocked` | Active work items, blockers, recent comments |

If a source does not exist or cannot be found, note "Source unavailable" inline
rather than omitting the section.

---

## Step 3 — Write the Briefing

### Required Sections (in this order)

#### 1. Project Status Summary

One row per active project. Required columns:

| Project | RAG | Owner | This Week | Next Milestone | Source |
|---------|-----|-------|-----------|----------------|--------|

**RAG rules (mandatory for every row):**
- 🟢 **Green** — on track, no blockers, next milestone is achievable
- 🟡 **Amber** — at risk: a blocker exists, milestone may slip, or key decision
  is pending
- 🔴 **Red** — blocked or significantly off track; needs Matt's attention this
  week

Never leave a RAG cell blank. If status is genuinely unknown, use 🟡 and note
"Status unclear — check with owner."

The **Source** column must cite the specific file or Paperclip issue that
informed the row (e.g., `01_Projects/Paperclip-Platform/index.md`,
`[TEC-1234](/TEC/issues/TEC-1234)`).

#### 2. Key Decisions Last Week

Bulleted list. Each bullet:
- **What was decided** — one line
- **Who decided / who was involved**
- **Source** — meeting note filename or Paperclip comment link

If no decisions were recorded, write: `None identified in sources reviewed.`

Do not list tentative discussions — only firm decisions.

#### 3. Active Risks & Blockers

Bulleted list. Each bullet:
- **Risk or blocker** — what it is
- **Project affected**
- **Rating** — High / Medium / Low (likelihood × impact)
- **Mitigation or owner** — who is handling it, or "Unmitigated"
- **Source** — register entry or Paperclip issue link

If nothing is open, write: `No active risks or blockers identified.`

Include both explicitly recorded risks AND blockers you can infer from the
`in_progress`/`blocked` Paperclip issues.

#### 4. Upcoming Milestones

Table of milestones due in the **next 14 days**:

| Milestone | Project | Due Date | Owner | On Track? |
|-----------|---------|----------|-------|-----------|

If nothing is due in 14 days, extend the window to 30 days and note that you
did so. If still nothing, write: `No milestones due in the next 30 days.`

#### 5. Items Requiring Matt's Attention

This is the most important section. Bullets only. Each item must be:
- Specific — name the project, issue, or decision
- Actionable — say what Matt needs to do (approve, decide, unblock, review)
- Linked — include a Paperclip issue link or vault path

If nothing requires Matt's attention, write: `Nothing requires immediate
attention this week.`

Do not add items here just to fill the section. A short list is better than a
padded one.

---

## Step 4 — File the Document

**Output path (canonical — do not vary):**
```
_Daily/Companions/<YYYY-WNN> Executive Briefing.md
```

Example: `_Daily/Companions/2026-W19 Executive Briefing.md`

**Document header:**
```markdown
# Executive Briefing — <YYYY-WNN>

*Generated: <YYYY-MM-DD> | Sources reviewed: <list sources consulted>*

---
```

**Link from daily note:** After writing the file, open the current day's daily
note (`_Daily/<YYYY-MM-DD>.md`) and add a line under the existing links or at
the top of the note:

```markdown
[[<YYYY-WNN> Executive Briefing]]
```

If the daily note does not exist, create a minimal one with just this link.

---

## Step 5 — Post a Paperclip Comment

After filing the document, post a comment on the execution issue with:

```
## Done

Weekly Executive Briefing filed.

- Document: `_Daily/Companions/<YYYY-WNN> Executive Briefing.md`
- Projects reviewed: <N>
- Items requiring Matt's attention: <N>
- Risks/blockers noted: <N>

Sources consulted: <list>
```

---

## Quality Checklist

Before marking the issue done, verify:

- [ ] Every project row has a RAG status
- [ ] Every data item cites a source (file path or issue link)
- [ ] The "Items Requiring Attention" section has only specific, actionable
  items — no vague entries
- [ ] The file is at exactly `_Daily/Companions/<YYYY-WNN> Executive Briefing.md`
- [ ] The current daily note links to the briefing
- [ ] All five required sections are present, even if some say "None identified"

---

## Edge Cases

**First run of the week (Monday morning):** The past 7 days' daily notes may
include weekend days with no entries — that's fine. Extract from whatever is
present.

**A project has no index file:** Note "No index file found" in the Source
column and use the most recent daily note mention to populate the row.

**Multiple routines fire the same week:** Check if a briefing for this ISO week
already exists at `_Daily/Companions/<YYYY-WNN> Executive Briefing.md`. If it
does, update the existing file rather than creating a duplicate — add a
`*Updated: <date>*` line to the header.

**Ad-hoc executive summary (not routine-triggered):** Follow the same template.
Use the current ISO week as the filename. If a briefing for this week already
exists, update it.

---

*TEC Custom Skill — maintained by the Deltek Technical Services Engineering team.*
