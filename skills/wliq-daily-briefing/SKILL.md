---
name: wliq-daily-briefing
description: Generates a personalised daily briefing for WLIQ PMs. Pulls tasks from Orbit, emails from Gmail, and meetings from Google Calendar — and responds inline in chat. Trigger by typing "today's agenda", "morning briefing", or "what's my day".
---

# WLIQ Daily Briefing Agent

## Purpose
Generate a personalised daily briefing for a WLIQ PM directly inside Claude.ai chat. Claude pulls data from Orbit, Gmail, and Google Calendar via MCP and responds inline. No scripts, no config, no Slack, no cron needed.

---

## Trigger Phrases
Activate when the user says any of:
- "today's agenda" / "agenda for today" / "my agenda"
- "what's my day" / "day plan" / "show me my day"
- "daily briefing" / "morning briefing" / "briefing"
- "what do I have today" / "what's on today"
- "show me my tasks today" / "what are my tasks today"

Do NOT activate for specific task or project queries — only full day/agenda requests.

---

## Execution Steps

### STEP 1 — Acknowledge immediately
Reply with a single line so the PM knows it's running:
```
Got it — pulling your briefing now... ⏳
```

---

### STEP 2 — Get today's date and day
Use the user's local timezone if detectable, otherwise IST.
If Saturday or Sunday, respond:
```
It's the weekend — no briefing today. Enjoy! 🙌
```
Unless the user explicitly asks for one.

---

### STEP 3 — Pull calendar events
Use Google Calendar MCP: `gcal_list_events`
- Time range: today 00:00 to today 23:59
- Exclude: "Out of Office", "OOO", "Holiday", "Leave", "Birthday", all-day blocks, declined events
- Sort chronologically
- Format: `[TIME] — [TITLE] ([DURATION]) · [N attendees if > 1]`
- If none: "No meetings scheduled today"

---

### STEP 4 — Pull all Orbit tasks assigned to this user
Use Orbit MCP: `get_my_task_list` with `is_completed: incomplete`

Capture per task:
- Task ID, title, project name, status, due date, severity, comment count

Do NOT filter yet — collect everything first.

---

### STEP 5 — Fetch comment chains for tasks that have comments
For every task where comment count > 0, call Orbit MCP: `list_task_comments`

Read all comments oldest to newest. For each chain note:
- What was discussed overall (2-3 sentences)
- What the latest comment says and who said it
- Whether PM action is needed: Yes/No — and exactly what

---

### STEP 6 — Pull today's emails
Use Gmail MCP: `gmail_search_messages`

Query:
```
after:[TODAY_DATE] -from:me -category:promotions -category:social
```

For each email extract: sender name, email, subject, full body/snippet. Match to an Orbit project if possible.

Do NOT filter yet — collect all emails first.

---

### STEP 7 — Build and render the briefing as an interactive visual dashboard

After collecting all data, output a brief single-line acknowledgement, then render the full briefing using the `visualize:show_widget` tool as an HTML dashboard. Do NOT output a markdown briefing — the widget IS the briefing.

**Before calling show_widget, call `visualize:read_me` with modules `["mockup"]` to load design tokens.**

#### Dashboard structure (HTML widget)

The widget must contain these sections, rendered as styled cards:

**Header row**
- "Good morning/afternoon/evening, [FIRST NAME]" (20px, weight 500)
- Date badge (e.g. "Monday, 14 Mar 2026 · IST")
- Weekend badge if applicable (amber)

**Metric strip** — 4 stat cards in a row:
- Open tasks (count)
- Emails today (count)
- Meetings today (count)
- Action needed (count of priority items, colored danger if > 0)

**OOO bar** (info color) — show if an Out of Office block exists today

**Section: ⚡ Needs your attention**
Cards with a red left border (`border-left: 2px solid var(--color-text-danger)`).
Include tasks/emails where ANY of:
- Severity = Critical or Important
- Due date = today or overdue
- Comment thread has PM action needed
- Email contains a question, request, or deadline from client/manager

Each attention card shows:
- Title, sender/project, body (2–3 sentences), action label in red

**Section: 📋 Open tasks**
One card containing all tasks grouped by project.
Each task row: task name + project name (secondary, smaller) on left, status badge on right, Orbit link (↗) as small hyperlink.

Status badge colors:
- Doing → warning (amber)
- To Do → info (blue)
- Done (not completed) → success (green) with asterisk note
- Backlog → tertiary gray
- Reference → secondary gray with border

Add a footnote: `* Done but not marked complete in Orbit — worth cleaning up.`

**Section: 📬 Other emails (FYI)**
Cards with a gray left border. One card per email — sender, subject, 1–2 sentence summary. Mark system/automated emails with reduced opacity (0.7).

**Footer**
`Have a focused day, [FIRST NAME]. 🎯` centered, 12px, tertiary color.

#### Design rules for this widget

- Use CSS variables throughout — never hardcode colors
- Status badges: `display:inline-block; font-size:11px; padding:2px 8px; border-radius:20px; font-weight:500`
- Task rows: `display:flex; align-items:center; justify-content:space-between; gap:12px; padding:8px 0; border-bottom:0.5px solid var(--color-border-tertiary)`
- Cards: `background:var(--color-background-primary); border:0.5px solid var(--color-border-tertiary); border-radius:var(--border-radius-lg); padding:1rem 1.25rem; margin-bottom:10px`
- Metric cards: `background:var(--color-background-secondary); border-radius:var(--border-radius-md); padding:0.75rem 1rem`
- No gradients, no shadows, no emoji in CSS — emoji allowed in text only
- All task Orbit links open via `<a href="[URL]" target="_blank">↗</a>` styled as `color:var(--color-text-info); font-size:11px; text-decoration:none`
- Dark mode is automatic via CSS variables — no media queries needed

#### Loading messages for show_widget

Use 3 loading messages, e.g.:
```
["Pulling your day together", "Sorting tasks and emails", "Rendering your briefing"]
```

#### Widget title

Use snake_case: `wliq_daily_briefing_[YYYYMMDD]` (e.g. `wliq_daily_briefing_20260314`)

---

After the widget renders, add one short line of plain text below it (outside the tool call) if there are notable items to call out — e.g. "5 tasks marked Done but not closed in Orbit." Keep it to 1–2 sentences max.

---

## Error Handling
- Orbit MCP fails → show "⚠️ Could not load Orbit — check Orbit is connected in Claude.ai Settings → Integrations" and continue
- Gmail MCP fails → show "⚠️ Could not load Gmail — check Gmail is connected in Claude.ai Settings → Integrations" and continue
- Calendar MCP fails → skip schedule section silently, note it at top
- Always deliver whatever data was collected — partial briefing beats nothing

---

## Required MCP Connections
These must be connected in Claude.ai Settings → Integrations:
- **Orbit:** `https://orbitmcp.whitelabeliq.net/mcp/orbit`
- **Gmail:** `https://gmail.mcp.claude.com/mcp`
- **Google Calendar:** `https://gcal.mcp.claude.com/mcp`
