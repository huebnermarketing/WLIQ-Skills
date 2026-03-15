---
name: orbit-weekly-report
description: Generates a comprehensive weekly report for any WLIQ matrix team — auto-detecting the requester's matrix from Notion, pulling all member + lead workload from Orbit, and rendering a full interactive dashboard with completed work, in-progress, blocked, overdue, due next week, per-member breakdown, project health, and a manager-ready summary. Trigger phrases include "weekly report", "week in review", "weekly summary", "what did the team do this week", "weekly update", "generate weekly report", "team report for this week", "how did the team do this week". If no matrix is specified, ask which matrix before proceeding.
---

# Orbit Weekly Report

## Purpose
Generate a comprehensive, team-scoped weekly report by:
1. Auto-detecting the requester's matrix from Notion
2. Pulling full workload data for every team member + the lead from Orbit
3. Rendering a detailed interactive dashboard covering the full week — past and forward

---

## Trigger Phrases
- "weekly report" / "week in review" / "weekly summary"
- "what did the team do this week"
- "weekly update" / "generate weekly report"
- "team report for this week"
- "how did the team do this week"

**If no matrix name is given → ask:**
```
Which matrix's weekly report would you like? e.g. SaaS, AI, Matrix A, Matrix B, Maintenance...
```

---

## Execution Steps

### STEP 1 — Acknowledge
```
Generating weekly report for [MATRIX]... ⏳
```

Compute current week dates (IST, UTC+5:30):
- Week start = last Monday (YYYY-MM-DD)
- Week end = this Sunday (YYYY-MM-DD)
- Next week start = this Monday + 7 days
- Next week end = next Sunday

---

### STEP 2 — Detect requester's matrix from Notion
Fetch Notion Matrix Detail page: `2e63846840c880e0a084f983351d943c`

- If user provided a matrix name → find that matrix
- If not → find which matrix the requester is Lead or member of
- Extract: matrix name, lead, all members grouped by role

**If requester is in multiple matrices → ask which one.**

---

### STEP 3 — Resolve all members to Orbit IDs
Call `list_users` once. Match all Notion member names → Orbit user IDs.
Include the lead's Orbit ID too.
Mark unresolved as "Not in Orbit".

---

### STEP 4 — Pull data for every member + lead

For each resolved member/lead, make these calls:

**A) All incomplete tasks:**
`get_user_workload` with `is_completed=incomplete`, `per_page=500`
→ captures: open, overdue, due_today, due_this_week, no_due_date, open_estimated_hours, by_status, by_project

**B) Completed tasks (Done status — used as "completed this week" proxy):**
`get_user_workload` with `is_completed=completed`, `per_page=500`
→ captures all Done tasks — report them as completed work
⚠️ Note: No completion date available — these are all Done tasks, not strictly this week only

**C) Tasks due next week:**
`get_user_workload` with `is_completed=incomplete`, `due_date_from=[next_week_start]`, `due_date_to=[next_week_end]`
→ forward look for upcoming deadlines

**D) Lead's owned projects:**
`list_projects` with `project_owner_id=[lead_orbit_id]`, `status_name=Active`
→ all active projects the lead owns

**E) Project summaries:**
For each unique project found across all members' `by_project`:
`get_project_summary`
→ health snapshot per project

---

### STEP 5 — Aggregate team-level data

From all member data compute:

**Totals:**
- Total Done tasks across team
- Total open tasks
- Total overdue tasks
- Total tasks due next week
- Total open estimated hours
- Total tasks with no due date

**Per member:**
- Done count / Open count / Overdue count / Est. hours / Load level
- Top 2 projects they're working on
- Risk flags (overdue / no due dates / spread thin)

**By project:**
- Which projects have the most team activity
- Completion % per project (from get_project_summary)
- Health status per project

**Load level per member:**
- 🔴 Overloaded: overdue > 0 OR open_estimated_hours > 300
- 🟡 Busy: open_tasks ≥ 5 OR open_estimated_hours 100–300
- 🟢 Available: open_tasks < 5 AND est_hours < 100 AND overdue = 0

---

### STEP 6 — Build and output

**Always output THREE things:**
1. **HTML artifact** (full interactive dashboard)
2. **Inline summary** (6-8 bullets)
3. **Manager-ready copy-paste block** (Slack/email format)

---

## 📊 Artifact Spec — Weekly Report Dashboard

HTML + Chart.js CDN. Data hardcoded as constants at top.

```html
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
```

---

### HEADER
```
[MATRIX NAME] — Weekly Report
Week of [MON DATE] – [SUN DATE]           [TEAM HEALTH BADGE]
Lead: [NAME] · [N] members
```

---

### STAT PILLS (6 pills)
| Pill | Color |
|---|---|
| Done Tasks | green |
| Open Tasks | amber |
| Est. Hours Remaining | cyan |
| Overdue | red |
| Due Next Week | blue |
| No Due Date | gray |

---

### 6 TABS

**Tab 1 — This Week**
Split into two panels:

LEFT — Done Tasks (grouped by member):
- Member name + role badge + count of Done tasks
- Expandable: list of Done task titles under each member
- Sorted by Done count descending

RIGHT — Activity Chart:
- Stacked bar per member: Done (green) / Open (amber) / Overdue (red)
- Shows who was most productive this week

**Tab 2 — In Progress**
Table view of all active (non-done, non-hold) tasks across team:
| Member | Task Title | Project | Status | Due Date | Est. Hrs |
- Color-code due date: red if overdue, amber if due this week, gray if no date
- Sorted by due date ascending (overdue first)

**Tab 3 — Blocked / On Hold**
Cards for all Hold-status tasks:
- Member · Task title · Project · How long on hold (if start_date available)
- Recommendation per blocked item

**Tab 4 — Overdue**
Red-bordered cards, one per overdue task:
- Member · Task · Project · Due date · Days overdue
- Sorted by most overdue first

**Tab 5 — Next Week**
Tasks due in the coming week — forward look:
| Member | Task | Project | Due Date | Est. Hrs |
- Green if available, amber if busy, red if already overloaded

**Tab 6 — Team Snapshot**
Two panels:

LEFT — Per Member Summary Table:
| Member | Role | Done | Open | Overdue | Est. Hrs | Load |
- Color-coded load badge per row
- Mini bar showing open tasks proportionally

RIGHT — Project Health:
| Project | % Done | Open | Overdue | Health |
- One row per project matrix members are on
- Progress bar per project

---

### Manager Summary Section (always visible below tabs)
Auto-generated, formatted for copy-paste to Slack or email:

```
📋 [MATRIX] Weekly Report — [DATE RANGE]

✅ Completed: [N] tasks done across [N] projects
   • [Top 3 notable completions]

🔄 In Progress: [N] tasks active across team
   • [Key items in flight]

🔴 Needs Attention: [N] overdue · [N] blocked
   • [Top risks with owner names]

📅 Coming Up Next Week: [N] tasks due
   • [Key upcoming items]

👥 Team Load: [N] overloaded · [N] busy · [N] available
   • [Any capacity concerns]
```

"Copy to clipboard" button below this block.

---

### Design tokens (consistent with other skills)
```
bg-page:  #030712 | bg-card: #111827 | bg-inner: #1f2937
accent:   #06b6d4 | green: #10b981 | amber: #f59e0b | red: #ef4444
font: 'DM Sans'   | mono: 'DM Mono'
```

Role badges: Lead/PM=cyan · Dev=blue · QA=violet · HTML=amber · Designer=pink · Other=gray
Load badges: Overloaded=red · Busy=amber · Available=green

---

### Inline summary (after artifact)
```
**[MATRIX] Weekly Report — [DATE RANGE]**
• ✅ [N] tasks marked Done across [N] projects
• 🔄 [N] tasks currently in progress · [N] est. hours remaining
• 🔴 [N] overdue tasks — [names] need attention
• ⏸ [N] tasks blocked/on hold
• 📅 [N] tasks due next week
• 👥 Team load: [N] overloaded / [N] busy / [N] available
• → [Top recommendation for the lead]
```

---

### Manager copy-paste block (after inline summary)
Plain text block ready for Slack/email with copy button.

---

## ⚠️ Completion Date Limitation
Done tasks are reported without verified completion dates — Orbit does not expose a `completed_at` timestamp. All "Done" status tasks are shown as completed work. For more precision, task comments may contain developer-logged completion notes.

---

## Error Handling
- Notion fetch fails → ask user to name their matrix manually, continue with Orbit only
- Member not in Orbit → show in "Not in Orbit" section, exclude from charts
- `get_project_summary` fails → skip project health section, note it
- Always deliver partial data — never fail silently

---

## Required MCP Connections
- **Notion:** `https://mcp.notion.com/mcp`
- **Orbit:** `https://orbitmcp.whitelabeliq.net/mcp/orbit`

## Tools Used
- `notion-fetch` — live matrix team structure
- `list_users` — resolve member names to Orbit IDs
- `get_user_workload` (×3 per member) — incomplete / completed / due next week
- `list_projects` — lead's owned projects
- `get_project_summary` — project health per active project
