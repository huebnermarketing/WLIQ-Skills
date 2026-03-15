---
name: orbit-client-360
description: Generates a full 360° client overview across ALL active projects for any named WLIQ client — aggregating health, tasks, team, and flags in one interactive dashboard. Use this skill whenever the user asks about a client broadly, wants to see everything under a client, or needs a pre-meeting/pre-call briefing. Trigger phrases include "catch me up on [client]", "360 on [client]", "everything on [client]", "client overview for [client]", "prep me for [client] call", "what's the state of [client]", "all projects for [client]", "how are we doing with [client]". Also trigger when the user mentions a client name alongside words like "overview", "status", "everything", "all projects", or "briefing".
---

# Orbit Client 360

## Purpose
Generate a deep, comprehensive client overview across ALL active Orbit projects — pulling project summaries, full task lists, assignee breakdowns, quoted hours, overdue context, and cross-project team view — rendered as a rich interactive dashboard. Built for pre-call prep, account reviews, and director-level visibility.

---

## Trigger Phrases
- "catch me up on [client]" / "360 on [client]"
- "client overview for [client]" / "everything on [client]"
- "prep me for [client] call"
- "what's the state of [client]"
- "all projects for [client]"
- "how are we doing with [client]"

Do NOT activate for single-project queries — use `orbit-project-pulse` for those.

---

## Execution Steps

### STEP 1 — Acknowledge immediately
```
Pulling 360° view for [client]... ⏳
```

---

### STEP 2 — Resolve the client and get all projects
Call `list_projects` with `search_value` = client name.
- Get ALL pages if total > 20
- If multiple client names match → list them, ask which one
- Separate Active / On Hold / Closed — focus on Active for main view

**Never invent IDs. Always resolve from search.**

---

### STEP 3 — Pull project summaries for all active projects
For each active project call `get_project_summary`.

Capture:
- total, done, open (= total - done)
- by_status[] breakdown
- by_assignee[] breakdown
- is_overdue, quoted_hours, owner, start_date, due_date

---

### STEP 4 — Pull FULL task list for every active project
For each active project call `get_project_task_list` with:
- `is_completed` = "all"
- `per_page` = 500

For each task capture:
- id, title, assignee, status, severity, start_date, due_date, estimated_hours, comment_count

This is the key difference from v1 — we pull actual task titles and details, not just counts.

---

### STEP 5 — Score health per project

| Condition | Health |
|---|---|
| is_overdue = true | 🔴 Blocked |
| open = 0 AND total > 0 | 🟢 Complete |
| >30% open tasks unassigned | 🔴 Blocked |
| 10–30% unassigned OR completion < 20% with due date set | 🟡 At Risk |
| Everything else | 🟢 Healthy |

**Overdue context:** If is_overdue = true, calculate days overdue = today - due_date.
List the open tasks causing the delay (non-Done, non-Reference tasks).

**Quoted hours utilization:** If quoted_hours > 0, compute:
- utilization % = (sum of estimated_hours on all tasks / quoted_hours) * 100
- Flag if >90% utilized

**Overall client health** = worst health across all projects.

---

### STEP 6 — Build cross-project team view
From all tasks across all projects, aggregate by assignee:
- Which assignees appear across multiple projects
- Total task count per assignee across client
- Which projects each person is contributing to

---

### STEP 7 — Build and output

**Always output TWO things:**
1. **HTML artifact** (spec below)
2. **Inline summary** (5-7 bullets)

---

## 📊 ARTIFACT SPEC — LAYOUT CONTRACT (NON-NEGOTIABLE)

**ALWAYS render the same layout regardless of project count.**
Header → Stat Pills → Alert Banner (if overdue) → Tabs (6)

HTML + Chart.js CDN. All data hardcoded as constants at top.

```html
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
```

---

### HEADER
```
[CLIENT NAME]                                    [OVERALL HEALTH BADGE]
AM: [NAME]  ·  [N] active projects
```

### ALERT BANNER (show only if any project is overdue)
Red-tinted banner:
```
⚠ [N] projects are past their due date — [Project A] (overdue since [DATE]),
[Project B] (overdue since [DATE])...
```

### STAT PILLS (5 pills)
| Pill | Color |
|---|---|
| Active Projects | white |
| Overdue Projects | red |
| Total Open Tasks | amber |
| Total Done Tasks | green |
| Total Est. Hours | cyan |

---

### 6 TABS

---

**Tab 1 — Overview**
Two charts side by side:

LEFT — Stacked bar chart (Chart.js):
- One bar per project (top 10 by task count if >10)
- Stacked: Done (#10b981) / Open (#f59e0b) / Unassigned (#ef4444)
- Show project short names on X axis
- Custom HTML legend below (no Chart.js default)

RIGHT — Horizontal bar chart showing quoted hours vs estimated hours per project:
- Only show projects with quoted_hours > 0
- Two bars per project: Quoted (gray) / Used (cyan)
- If no projects have quoted hours → show completion % donut instead

---

**Tab 2 — Projects**
Sorted: Blocked → At Risk → Healthy → Complete.
One card per project. Always show ALL of:

```
[PROJECT TITLE]              [TYPE BADGE]  [HEALTH BADGE]  [OVERDUE BADGE]
#[project_number] · [type] · [quoted_hrs] hrs quoted · Started [start_date]
Owner: [NAME] · Due: [DATE in red if overdue] · [N days overdue if applicable]

[STAT ROW]
Total: N  |  Done: N  |  Open: N  |  Unassigned: N  |  Client Review: N  |  Hold: N

[PROGRESS BAR — completion %]  N% complete

[STATUS BREAKDOWN — horizontal mini bars]
Done        [████████████░░░░░] N
To Do       [████░░░░░░░░░░░░░] N
Client Review [██░░░░░░░░░░░░░░] N
Hold        [█░░░░░░░░░░░░░░░░] N
Unassigned  [██░░░░░░░░░░░░░░░] N

[ASSIGNEE PILLS]
[Name (N tasks)]  [Name (N tasks)]  [Name (N tasks)] ...

[▶ EXPAND] → shows actual open task list:
  • [Task title] — [Assignee] · [Status] · Due: [DATE or —] · [Est hrs]h
  (sorted: overdue first, then by status)
```

For >10 projects: compact card style (padding: 10px, font-size: 12px)

---

**Tab 3 — All Tasks**
Full flat task table across ALL projects, all statuses.

Columns: Project | Task Title | Assignee | Status | Severity | Due Date | Est. Hrs

Filters row at top (clickable pills to filter):
- All | Open | Done | Overdue | Unassigned | Client Review | Hold

Color rules:
- Overdue due date → red text
- Due this week → amber text
- Unassigned → amber assignee cell
- Critical severity → red severity badge

Sort: overdue first → due soonest → no date last

---

**Tab 4 — Team**
Cross-project team breakdown.

Table: Assignee | Projects they're on | Total tasks | Done | Open | Overdue
- Show which specific projects each person contributes to (as small badges)
- Sort by total tasks desc
- Highlight anyone with tasks in 3+ projects (spread thin)
- Highlight anyone with overdue tasks

Below table: stacked bar chart — top 8 assignees, bars stacked by project

---

**Tab 5 — Flags**
All issues that need PM attention, sorted by severity.

Categories:
- 🔴 OVERDUE — project past due date, show days overdue + open tasks blocking it
- 🔴 UNASSIGNED — tasks with no assignee (group by project)
- 🟡 CLIENT REVIEW — tasks waiting on client, show how long (start_date)
- 🟡 HOLD — tasks on hold, show how long
- 🟡 NO DUE DATE — open tasks with no due date (count per project)
- 🟠 HOURS — projects >90% of quoted hours utilized

Each flag card: left color border, project name, description, recommendation

---

**Tab 6 — Talking Points**
Two sections:

✅ CLIENT-SAFE (green cards) — positive bullets:
- Completed work counts
- Items in client review (shows you're waiting on them)
- Upcoming deliverables
- Progress % on major projects

⚑ INTERNAL ONLY (gray section) — risks not to share:
- Overdue project details
- Unassigned task counts
- Resource spread issues
- Hours utilization warnings

Button: `sendPrompt('Draft a client status email for [CLIENT]')` → "Draft status email ↗"
Button: `sendPrompt('What should I discuss in my call with [CLIENT]?')` → "Call prep ↗"

---

### Design tokens
```
bg-page:  #030712 | bg-card: #111827 | bg-inner: #1f2937
accent:   #06b6d4 | green: #10b981 | amber: #f59e0b | red: #ef4444 | blue: #3b82f6
font: 'DM Sans'   | mono: 'DM Mono'
border: rgba(255,255,255,0.08)
```

Type badges: SaaS=cyan · Maintenance=violet · Fixed Cost=amber · Hosting=gray · Ad-hoc=slate
Health badges: Blocked=red · At Risk=amber · Healthy=green · Complete=teal
Status colors: Done=green · To Do=blue · Client Review=purple · Hold=gray · Unassigned=red · Reference=slate

Quoted hours bar: gray=quoted, cyan=used, red fill if >90% used

Progress bar color: ≥70%=green · 30–69%=amber · <30%=red

Chart canvas rules:
- Always wrap in `<div style="position:relative;width:100%;height:220px">`
- `responsive:true, maintainAspectRatio:false` on all charts
- `plugins:{legend:{display:false}}` — use custom HTML legends always

---

### Scaling rules (>10 active projects)
- Overview chart: top 10 by task count + "Other (N)" aggregate
- Projects tab: ALL cards in compact mode
- Tasks tab: ALL tasks, filterable
- Team tab: top 10 by task count
- Flags tab: ALL flags, no limit
- Stats: always aggregate ALL projects

---

## Inline Summary (after artifact)
```
**[CLIENT] 360 — [DATE]**
• [N] active projects — overall: [🟢/🟡/🔴]
• [N] open tasks · [N] done · [N] unassigned
• 🔴 [Key risk with specifics — project name, days overdue]
• 🟡 [Client review / hours / hold items]
• ✅ [Notable completion or progress]
• → [Top recommendation]
```

---

## Error Handling
- Client not found → list closest matches, ask user to confirm
- `get_project_task_list` fails → fall back to summary data only, note the gap
- `get_project_summary` fails → skip project, note it
- Always deliver partial data

---

## Required MCP
- **Orbit:** `https://orbitmcp.whitelabeliq.net/mcp/orbit`

## Tools Used
- `list_projects` — all projects by client
- `get_project_summary` — task counts + assignee breakdown per project
- `get_project_task_list` — full task list with titles, statuses, assignees per project
