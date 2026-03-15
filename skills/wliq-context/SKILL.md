---
name: wliq-context
description: >
  Master context skill for White Label IQ (WLIQ). Loads Tarang Chokshi's
  full organisational context — team directory, Orbit IDs, client domains,
  Fathom teams, tool routing logic, and output formats — into every Claude
  session automatically. Activate with phrases like "catch me up on [client]",
  "prep me for [meeting]", "what's pending", "team workload", "draft email to
  [person]", "create task for [description]", or "what happened this week".
  Also activates whenever a query references a WLIQ client, team member, or
  internal project by name.
---

# WLIQ Master Context Skill

## Purpose

This skill gives Claude complete, accurate knowledge of White Label IQ's
people, tools, clients, and conventions — so every session is immediately
operational. No warm-up. No repeated context. Claude knows who you are,
who your team is, which tool to call for each query, and how to format
every output consistently.

---

## Section 1 — Who You Are

**User:** Tarang Chokshi
**Email:** tarangc@whitelabeliq.com
**Role:** Director of Product Development & Head of SaaS & AI Innovation
**Orbit user ID:** 38
**Timezone:** Asia/Kolkata (IST, UTC+5:30)

**Reports to:**
- Nishant Rana — Managing Director | Orbit ID: 4 | nishantr@whitelabeliq.com
- Brian Gerstner — President | Orbit ID: 3 | briang@whitelabeliq.com

**Teams you lead:**
- **SaaS Matrix** — Laravel, Vue.js, full-stack developers + QA
- **AI Matrix** — AI & Automation Strategists

**Philosophy:** Innovate with purpose, execute with precision, deliver with impact.

---

## Section 2 — Key People Directory

### Executive & Leadership

| Name | Orbit ID | Email | Role |
|------|----------|-------|------|
| Brian Gerstner | 3 | briang@whitelabeliq.com | President |
| Nishant Rana | 4 | nishantr@whitelabeliq.com | Managing Director |
| Tarang Chokshi | 38 | tarangc@whitelabeliq.com | Dir. Product Dev & AI |
| Manan Rana | 26 | mananr@whitelabeliq.com | Super Admin |
| Manthan Rana | 27 | manthanr@whitelabeliq.com | Super Admin (data/infra) |

### Customer Success (CS) Team

| Name | Orbit ID | Email | Fathom Team |
|------|----------|-------|-------------|
| Zeel Gadhavi | 1097 | zeelg@whitelabeliq.com | Customer Success |
| Hiten Upadhyay | 587 | hitenu@whitelabeliq.com | Customer Success |
| Dhruvi Chandarana | 965 | dhruvic@whitelabeliq.com | Customer Success |
| Caitlin Sims (Reale) | 81 | caitlinr@whitelabeliq.com | Customer Success |
| Jessica Provencher (Wagner) | 400 | jessicaw@whitelabeliq.com | Customer Success |
| Aagna Paneri | 12 | aagnap@whitelabeliq.com | Customer Success |
| Dhvani Chauhan | 1053 | dhvanic@whitelabeliq.com | Customer Success |
| Deepak Ravtani | 694 | deepakr@whitelabeliq.com | Operations |
| Ellen Thomas | 795 | ellent@whitelabeliq.com | Operations |

### Sales Team

| Name | Orbit ID | Email | Fathom Team |
|------|----------|-------|-------------|
| Ishant Kulshreshtha | 895 | ishantk@whitelabeliq.com | Sales |
| Aditi Singh | 935 | aditis@whitelabeliq.com | Sales |

### SaaS Matrix (Dev Team)

| Name | Orbit ID | Email |
|------|----------|-------|
| Pravin Kanzariya | 33 | pravink@whitelabeliq.com |
| Sunil Rathod | 927 | sunilr@whitelabeliq.com |
| Jay Panchal | 20 | jayp@whitelabeliq.com |
| Kishan Patel | 857 | kishanp@whitelabeliq.com |
| Mahendra Baku | 495 | mahendrab@whitelabeliq.com |
| Jash Chauhan | 1117 | jashc@whitelabeliq.com |
| Kiran Thakur | 1075 | kirant@whitelabeliq.com |
| Stafy Solanki | 1022 | stafys@whitelabeliq.com |

### AI Matrix

| Name | Orbit ID | Email |
|------|----------|-------|
| Abhishek Gray | 1095 | abhishekg@whitelabeliq.com |
| Jay Pandya | 1116 | jaypandya@whitelabeliq.com |
| Darshan Dagli | 1108 | darshan@whitelabelai.agency |

### Huebner Marketing (embedded client team in Orbit)

| Name | Orbit ID | Email |
|------|----------|-------|
| Jim Huebner | 8 | jimh@huebnermarketing.com |
| Ryan Carey | 2 | ryanc@huebnermarketing.com |

---

## Section 3 — Tool Routing Logic

When a query arrives, apply this routing table before making any tool call.
Use the first matching rule.

### Orbit MCP → for tasks, projects, workload

| Query type | Orbit tool to call |
|------------|--------------------|
| "what are my tasks / overdue" | `get_user_workload(user_id=38)` |
| "what is [person] working on" | `get_user_workload(user_id=[ID])` |
| "show tasks for [project]" | `get_project_task_list(project_id=[ID])` |
| "project health / summary" | `get_project_summary(project_id=[ID])` |
| "create a task for [desc]" | `create_task(project_id, title, assignee, task_status_id=24)` |
| "team workload / who's overloaded" | `get_user_workload` for each of: Pravin (33), Sunil (927), Kishan (857), Zeel (1097), Hiten (587) |

### Fathom MCP → for meetings, transcripts, action items

| Query type | Fathom tool to call |
|------------|---------------------|
| "recent meetings / calls" | `list_meetings(include_action_items=true)` |
| "calls with [client]" | `list_meetings(calendar_invitees_domains=["domain.com"])` |
| "find meeting about [topic]" | `search_meetings(query="[topic]")` |
| "open action items" | `list_meetings(include_action_items=true)` then filter `completed=false` |
| "summary of [meeting]" | `get_summary(recording_id)` |
| "transcript of [meeting]" | `get_transcript(recording_id)` |

### Gmail MCP → for emails, drafts, threads

| Query type | Gmail tool to call |
|------------|--------------------|
| "unread emails" | `gmail_search_messages(q="is:unread in:inbox")` |
| "emails from [client/person]" | `gmail_search_messages(q="from:domain.com")` |
| "emails about [topic/project]" | `gmail_search_messages(q="subject:[term]")` |
| "draft email to [person]" | Lookup email → `gmail_create_draft(to, subject, body)` |
| "read email / thread" | `gmail_read_message` or `gmail_read_thread` |

### Google Calendar MCP → for scheduling, meetings ahead

| Query type | Calendar tool to call |
|------------|-----------------------|
| "what's on my calendar" | `gcal_list_events(timeMin, timeMax, timeZone="Asia/Kolkata")` |
| "schedule a meeting" | `gcal_create_event(event={...})` |
| "prep for [meeting name]" | `gcal_list_events(q="[meeting name]")` + Fathom search |

### Pipedrive MCP → for deals, contacts, pipeline

| Query type | Pipedrive tool to call |
|------------|------------------------|
| "deals for [client]" | `search_deals_in_pipedrive(term="[client]", status="open")` |
| "find contact [name]" | `search_persons_in_pipedrive(term="[name]")` |
| "pipeline status" | `search_deals_in_pipedrive(status="open")` |

### Multi-tool queries — use these combinations

| Query | Tools to combine |
|-------|-----------------|
| "catch me up on [client]" | Fathom (domain filter) + Orbit (project tasks) + Gmail (from:domain) + Pipedrive (deals) |
| "prep me for [meeting]" | Calendar (get event details) + Fathom (search past calls) + Gmail (from:attendees) |
| "what happened this week" | Fathom (created_after: Mon) + Orbit (completed this week) + Gmail (this week) |
| "morning briefing / what's my day" | Calendar (today) + Orbit (overdue + due today, user_id=38) + Gmail (unread) + Fathom (open AIs) |
| "draft email about [project]" | Orbit (get task context) + Gmail (find thread if replying) + Gmail create_draft |

---

## Section 4 — Orbit Conventions

### Task status IDs (use these exactly — do not guess)

| Status name | ID |
|-------------|----|
| Quote | 21 |
| To Do | 24 |
| Doing | 25 |
| Done | 26 |
| Reference | 27 |
| Backlog | 28 |
| Unassigned | 29 |

**Default when creating a task:** `task_status_id = 24` (To Do)

### Severity IDs

| Severity | ID |
|----------|----|
| Critical | 15 |
| Important | 16 |
| Normal | 17 |

**Default when creating a task:** `severity_id = 17` (Normal)

### Known active projects (from Tarang's workload)

| Project name | Project ID |
|--------------|-----------|
| AI - Automation - Darshan Dagli | 7761 |
| WM4D - Hosting Servers Management | 6222 |
| Orbit Project Management | 6549 |
| White Label IQ Website | 4648 |
| Quarterly Rock - Oct-Dec 2025 | 7597 |
| OrbitAI - Orbit 2.0 Redevelopment | 7898 |
| IT Operations | 7685 |

> **Note:** For projects not in this list, call `list_projects` or `search` before
> creating a task. Never invent a project ID.

### Orbit API quirks to respect

- `due_date_filter: "today"` is unreliable vs. the Orbit UI. Use explicit
  `due_date_from` and `due_date_to` with today's date instead.
- Subtask creation requires **both** `parent_id` AND `project_id`.
- Max `per_page` is 500.
- `get_user_workload` returns a summary but task list may be empty — use
  `get_project_task_list` for full task details per project.

---

## Section 5 — Client Directory & Fathom Domain Filters

Use these domain mappings for Fathom `calendar_invitees_domains` filter and
Gmail `from:` queries. Never guess — use the exact domain.

| Client name | Domain | Notes |
|-------------|--------|-------|
| Whiteboard Marketing | whiteboard-mktg.com | Weekly standing call, Jana Leu |
| WestComm / Guy Westermeyer | westcomm.com | Brian's contact |
| ThinkDM2 / David Annunziato | thinkdm2.com | Dave, Chris Fuller, Alyssa |
| London Agency / John Emmerson | londonagency.com.au | Brian's contact |
| Hookset Agency / Best Fed Greek | hooksetagency.com | Mike Hannah |
| WM4D / David Herman | wm4d.com | Weekly status call Wed |
| Huebner Marketing | huebnermarketing.com | Embedded team in Orbit |
| Badpixel / Real Singles | badpixel.com | SaaS client |
| Collage Labs | collage.inc | SaaS client |
| WarneBenson / KMT | warnebenson.com | Scott Warne |
| Darshan / White Label AI | whitelabelai.agency | AI partner |

For **internal-only meetings** use: `calendar_invitees_domains_type: "only_internal"`
For **client calls** use: `calendar_invitees_domains_type: "one_or_more_external"`

---

## Section 6 — Fathom Teams

| Team name | Who uses it |
|-----------|-------------|
| Executive | Brian Gerstner |
| Sales | Ishant Kulshreshtha, Aditi Singh |
| Customer Success | Zeel, Hiten, Dhruvi, Caitlin, Jessica, Aagna, Dhvani |
| Engineering | Dev team members |
| Product | Tarang, Pravin, SaaS Matrix leads |
| Operations | Deepak, Ellen |
| Other | Misc / unassigned |

Use `teams: ["Customer Success"]` filter when looking for client call recordings.
Use `teams: ["Executive", "Sales"]` for BD and leadership calls.

---

## Section 7 — Output Formats

Claude must use these exact formats for consistency across every session.

### Morning briefing / "what's my day"

```
## Your day — [Weekday, Date] (IST)

### Calendar
[TIME] — [TITLE] ([DURATION]) · [N attendees]
...or: No meetings today.

### Overdue tasks ([N])
• [TASK TITLE] — [PROJECT] · [N days overdue]

### Open action items from recent calls
• [AI description] — assigned to [NAME] | [Meeting title, date]

### Priority emails
• [SENDER] — [SUBJECT] · [1-line summary of what they need]

### This week
[MEETING NAME] — [DAY TIME] · [N attendees]
```

### Client brief / "catch me up on [client]"

```
## [CLIENT NAME] — brief as of [DATE]

### Last call
[Meeting title] · [Date] · [Duration]
Attendees: [Names]
Open action items:
• [AI] → [Assignee]

### Open Orbit tasks ([N])
• [TASK] — [STATUS] · [ASSIGNEE] · [DUE DATE or "no due date"]

### Recent emails
• [DATE] [SENDER] — [SUBJECT]: [1-line summary]

### Pipedrive deal
Stage: [STAGE] · Value: [VALUE] · Last activity: [DATE]

### Next meeting
[EVENT TITLE] — [DATE TIME] · [ATTENDEES]
```

### Meeting prep / "prep me for [meeting]"

```
## Prep — [MEETING TITLE]
[DATE] [TIME] IST · [DURATION]

Attendees: [LIST]
Organised by: [NAME]

### Agenda
[From calendar description or inferred from context]

### Last call with these attendees
[Fathom summary or "No previous call found"]
Open AIs still pending: [LIST or "None"]

### Recent emails from attendees
• [DATE] — [SUBJECT]: [summary]

### Suggested talking points
• [Point based on open AIs or context]

### Suggested prep actions
• [Draft / review / confirm X before the call]
```

### Task creation confirmation (always confirm before creating)

```
Creating Orbit task:
Title: [TITLE]
Project: [PROJECT NAME] (ID: [ID])
Assignee: [NAME] (ID: [ID])
Status: To Do
Due: [DATE or "no due date"]
Severity: [LEVEL]

Confirm? (yes / adjust)
```

### Team workload summary

```
## Team workload — [DATE]

| Person | Open | Overdue | Doing | Projects |
|--------|------|---------|-------|----------|
| [NAME] | [N] | [N] | [N] | [LIST] |
```

---

## Section 8 — Guardrails (what NOT to do)

- **Never send emails** — only draft them. Always show the draft for review first.
- **Never delete tasks or events** without explicit double confirmation ("yes, delete it").
- **Never create a task** without confirming title, project, and assignee first.
- **Never invent an Orbit project ID** — look it up if not in the known list above.
- **Never use `due_date_filter: "today"`** — use explicit date range instead.
- **Never assume a client name maps to a Pipedrive org** without running a search first.
- **Never dump all 200 unread emails** — filter to actionable only; offer to show more.
- **Never run subtask creation without both `parent_id` AND `project_id`**.
- **Never attribute a Fathom call to the wrong client** — always verify via domain filter.
- **Never make up Orbit user IDs** — use the directory in Section 2 or call `list_users`.

---

## Section 9 — MCP Connections Required

These must be active in Claude.ai Settings → Integrations:

| Tool | MCP URL |
|------|---------|
| Orbit | https://orbitmcp.whitelabeliq.net/mcp/orbit |
| Fathom | https://www.fathom-mcp-server.com/mcp |
| Gmail | https://gmail.mcp.claude.com/mcp |
| Google Calendar | https://gcal.mcp.claude.com/mcp |
| Pipedrive | (Pipedrive MCP connector) |

---

## Section 10 — Error Handling

| Failure | Response |
|---------|----------|
| Orbit MCP unreachable | Warn inline: "⚠️ Orbit not responding — check integration." Continue with other sources. |
| Fathom returns no results | Try `search_meetings` with broader query before giving up. |
| Gmail rate limit | Reduce `maxResults`, retry once. |
| Calendar returns no events | Confirm date range is correct. Check timezone = Asia/Kolkata. |
| Pipedrive finds no deal | Try `search_persons_in_pipedrive` as fallback for contact lookup. |
| Unknown project name | Call `list_projects` or ask user to confirm project before creating task. |
| Partial data only | Always deliver what was retrieved — partial brief beats nothing. |

---

## Section 11 — Activation Examples

The following show exactly what Claude does per trigger phrase:

**"morning briefing"**
→ Calendar (today IST) + Orbit workload (user_id=38, overdue) + Gmail (unread, filter noise) + Fathom (open AIs last 5 meetings). Output: morning briefing format.

**"catch me up on Whiteboard Marketing"**
→ Fathom (domain: whiteboard-mktg.com, include_action_items: true) + Orbit (search project) + Gmail (from:whiteboard-mktg.com last 14 days) + Pipedrive (search deals). Output: client brief format.

**"prep me for the AI Webinar meeting Monday"**
→ Calendar (search "AI Webinar") + Fathom (search "AI Webinar" + "webinar") + Gmail (from:aditis@whitelabeliq.com). Output: meeting prep format.

**"what's pending / overdue"**
→ Orbit (get_user_workload, user_id=38, due_date_filter overdue) + Fathom (list_meetings, include_action_items, filter completed=false). Output: bulleted overdue list with source tags.

**"create a task for reviewing the HAVIS middleware approach"**
→ Confirm: project = Huebner Marketing (look up ID), assignee = Tarang or specified person, status = To Do. Show confirmation card. Wait for "yes" before calling create_task.

**"draft email to Zeel about the Chatter Stripe Integration comment"**
→ Lookup Zeel's email (zeelg@whitelabeliq.com from directory). Search Gmail for Chatter Stripe thread. Draft reply with context. Show draft for review. Do NOT send.

**"team workload"**
→ get_user_workload for: Pravin (33), Sunil (927), Kishan (857), Zeel (1097), Hiten (587), Deepak (694). Output: workload table format.

**"what happened this week"**
→ Fathom (created_after: Mon 00:00 UTC, include_action_items: true) + Orbit (completed tasks, user_id=38, this week) + Gmail (after:Mon, from:client domains). Output: weekly digest — meetings held, tasks completed, key emails, open AIs still pending.

---

*Last updated from live data pull: Saturday 14 March 2026.*
*Maintain this file in the `@tarangchokshi/claude-skills-cli` package.*
*Push updates whenever team structure, client list, or Orbit project IDs change.*
