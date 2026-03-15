---
name: orbit-team-workload
description: Shows live workload and capacity for any named WLIQ matrix team — fetching team structure live from Notion and pulling each member's tasks from Orbit. Use whenever someone asks about a team's workload, capacity, who is overloaded, what a team is working on, or how a specific matrix is doing. Trigger phrases include "how is [matrix/team] doing", "workload for [team]", "how is [team] team doing", "who's overloaded in [matrix]", "capacity check for [team]", "[team] matrix workload", "what is [team] working on". If no matrix name is provided, ALWAYS ask the user which matrix they mean before proceeding — never guess or show all matrices.
---

# Orbit Team Workload

## Purpose
Pull live matrix team structure from Notion and cross-reference with Orbit workload data for every member — delivering a rich, interactive per-matrix dashboard. Always fresh — team changes in Notion are reflected automatically on every run.

---

## Trigger Phrases
- "how is [matrix] doing" / "how is [team] team doing"
- "workload for [team]"
- "capacity check for [team]"
- "[team] matrix workload"
- "what is [team] working on"
- "who's overloaded in [team]"
- "show me [team] workload"

**If no matrix name is provided → STOP and ask:**
```
Which matrix would you like to check? For example: SaaS, AI, Matrix A, Matrix B, Maintenance, WIC...
```
Never proceed without a specific matrix name.

---

## Execution Steps

### STEP 1 — Acknowledge
```
Pulling [MATRIX NAME] team workload... ⏳
```

---

### STEP 2 — Fetch live team structure from Notion
Always fetch the Matrix Detail page fresh — never use hardcoded team data.

**Notion page ID: `2e63846840c880e0a084f983351d943c`**

Call `notion-fetch` with this ID.

Parse the page to find the matrix matching the user's request:
- Fuzzy match: "SaaS" → "SaaS / Web Application Matrix", "Matrix A" → "Matrix A - Abhishek", "AI" → "AI Matrix"
- Extract: matrix full name, lead name(s), all members grouped by role
- If no match → reply: "I couldn't find '[name]'. Available matrices: [list all from page]"

**Known matrices (for matching reference only — always fetch live):**
Matrix A (Abhishek), Matrix B (Hiten), Matrix C (Dhruvi), Matrix D (Dhvani),
Marketing + SEO, Internal Projects, Floater/Shared Resources, Quoting/Costing,
Maintenance, WIC Dedicated, AI Matrix, SaaS/Web Application,
Night Shift, Hosting + System Admin, HR, Sales, Finance

---

### STEP 3 — Resolve member names to Orbit user IDs
Call `list_users` once to get the full user list.
For each member name from Notion → match by first name or full name.
Build map: `{ notion_name: "Kishan", orbit_id: 857, full_name: "Kishan Patel" }`
Mark unresolved members as "Not in Orbit" — skip workload pull but still show on dashboard.

---

### STEP 4 — Pull workload for every resolved member
For each resolved member, call `get_user_workload`:
- `user_id` = Orbit ID
- `is_completed` = "incomplete"
- `per_page` = 500

Capture per member:
- `open_tasks`, `overdue_tasks`, `due_today`, `due_this_week`, `no_due_date`
- `open_estimated_hours`
- `by_status[]`, `by_severity[]`, `by_project[]`

---

### STEP 5 — Derive capacity signals

**Load level per member:**
- 🔴 Overloaded: overdue > 0 OR open_estimated_hours > 300
- 🟡 Busy: open_tasks >= 5 OR open_estimated_hours 100–300
- 🟢 Available: open_tasks < 5 AND open_estimated_hours < 100 AND overdue = 0

**Risk flags per member:**
- Has overdue tasks
- >50% tasks have no due date
- Assigned across 3+ projects (spread thin)

**Team aggregates:**
- Total open tasks, total estimated hours, total overdue
- Top project consuming most team bandwidth
- Overall team health = worst individual level

---

### STEP 6 — Build and output

**Always output TWO things:**
1. **HTML artifact** (spec below)
2. **Inline summary** (5-6 bullets)

---

## 📊 Artifact Spec — Matrix Workload Dashboard

HTML + Chart.js via CDN. All data hardcoded as constants at top of file.

```html
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
```

---

### HEADER
```
[MATRIX FULL NAME]                          [HEALTH BADGE]
Lead: [NAME] · [N] members · [role summary e.g. "3 Dev · 1 QA · 1 HTML"]
```

### STAT PILLS (5, always aggregate across full matrix)
Members | Total Open Tasks (amber) | Est. Hours (cyan) | Overdue (red) | No Due Date (gray)

---

### 4 TABS

**Tab 1 — Overview**
- LEFT chart: Horizontal bar per member — open tasks count. Bar color: red=overloaded, amber=busy, green=available
- RIGHT chart: Stacked bar — top 5 projects consuming matrix tasks (aggregated from all members' by_project)
- Custom HTML legends (no Chart.js default)

**Tab 2 — Members**
Sorted: Overloaded → Busy → Available. One card per member:
- Name · Role badge · Load badge (🔴/🟡/🟢)
- Stats: Open · Overdue · Est. Hrs · No Due Date
- Mini proportional bar (open tasks vs team max)
- Top 2 projects they're on
- Risk flags inline if any

**Tab 3 — Projects**
Table view — aggregated from all members' by_project:
| Project | Members on it | Tasks | Est. Hours |
Sorted by task count desc. Shows which projects own this matrix's time.

**Tab 4 — Flags**
All risk flags across team. Left border: red=overdue, amber=no-due-dates, blue=spread-thin.
Each: member · role · issue · recommendation

---

### Design tokens
```
bg-page:  #030712  |  bg-card: #111827  |  bg-inner: #1f2937
accent:   #06b6d4  |  green:   #10b981  |  amber: #f59e0b  |  red: #ef4444
font: 'DM Sans'    |  mono: 'DM Mono'
```

Role badge colors: Lead/PM=cyan · Dev=blue · QA=violet · HTML=amber · Designer=pink · SEO=green · Other=gray

---

### Inline summary (after artifact)
```
**[MATRIX] Workload — [DATE]**
• [N] members · team health: [🔴/🟡/🟢]
• [N] open tasks · [N] est. hours · [N] overdue
• 🔴 [Key risk]
• 🟡 [Secondary concern]
• → [Top recommendation for lead]
```

---

### Not-in-Orbit note (if applicable)
```
⚠️ Not found in Orbit: [names] — workload data unavailable
```

---

## Error Handling
- Notion fetch fails → "⚠️ Could not load Notion. Check Notion MCP is connected in Settings → Integrations."
- Workload fetch fails for a member → mark "Data unavailable", continue with rest
- No members resolved → explain and suggest checking name spelling

---

## Required MCP Connections
- **Notion:** `https://mcp.notion.com/mcp`
- **Orbit:** `https://orbitmcp.whitelabeliq.net/mcp/orbit`

## Tools Used
- `notion-fetch` — live team structure from Matrix Detail page
- `list_users` — resolve names to Orbit IDs
- `get_user_workload` — per-member workload data
