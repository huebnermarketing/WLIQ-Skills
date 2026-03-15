---
name: orbit-project-pulse
description: Generates a rich, interactive project health card for any WLIQ Orbit project or client. Use this skill whenever the user asks about project status, project health, how a project is going, what's happening on a project, or references a specific client/project by name with intent to understand its current state. Trigger phrases include "pulse on [project/client]", "how is [project] doing", "project status for [client]", "what's the status of [project]", "health check on [project]", "what's happening with [client]", "show me [project]", "update on [project]". Also trigger when the user asks about open tasks, overdue tasks, or blocked items on a specific named project.
---

# Orbit Project Pulse

## Purpose
Generate a rich, at-a-glance project health card for any named WLIQ project or client — pulling live data from Orbit MCP and presenting it inline in chat. No dashboards, no tab-switching. Everything the PM needs to assess a project's health in one response.

---

## Trigger Phrases
Activate when the user says any of:
- "pulse on [project/client]"
- "how is [project] doing" / "how's [project] going"
- "project status for [client]"
- "what's the status of [project]"
- "health check on [project]"
- "what's happening with [client]"
- "show me [project]" (when context implies status, not task creation)
- "update on [project]"
- "open tasks on [project]"
- "what's blocked on [project]"

Do NOT activate for general task queries without a named project/client, or for task creation requests.

---

## Execution Steps

### STEP 1 — Acknowledge immediately
Reply with a single line:
```
Pulling project pulse... ⏳
```

---

### STEP 2 — Resolve the project
Extract the project or client name from the user's message.

**If a project name is mentioned:**
Call `list_projects` with `search_value` = the project name.
- If 1 result → proceed with that project
- If multiple results → list them and ask the user to pick one before continuing
- If 0 results → try `get_client_details` with `company_name` to find projects under that client

**If a client name is mentioned (no specific project):**
Call `get_client_details` with `company_name`.
- Get all active projects for that client
- If only 1 project → proceed
- If multiple → list them and ask which one, or offer "show all" option

**Never guess or invent project/client IDs.** Always look them up.

---

### STEP 3 — Pull project summary
Call `get_project_summary` with the resolved `project_id`.

Capture:
- Total tasks, completed tasks, incomplete tasks
- Task breakdown by status (To Do, In Progress, In Review, etc.)
- Task breakdown by assignee
- Any quoted hours vs. tracked (if available)

---

### STEP 4 — Pull full task list (incomplete only)
Call `get_project_task_list` with:
- `project_id` = resolved ID
- `is_completed` = "incomplete"
- `per_page` = 500

For each task capture:
- Task ID, title, assignee, status, severity, due date, comment count

---

### STEP 5 — Identify critical signals
From the task list, flag:

**🔴 Overdue** — due_date < today and not completed
**🟠 Critical/Important with no due date** — severity is Critical or Important but due_date is null
**🟡 Unassigned tasks** — no assignee set
**🔵 Stalled** — status is To Do or In Progress but no recent activity (infer from comment count = 0 if due date is past)

---

### STEP 6 — Fetch comments for flagged tasks
For every flagged task (overdue, critical, or unassigned) where comment_count > 0:
Call `list_task_comments` for that task.

For each comment chain note:
- What was discussed (1-2 sentences)
- Latest comment: who said it and what
- Whether PM action is needed: Yes/No — and exactly what

---

### STEP 7 — Build and output the Project Pulse card

**Always output TWO things:**
1. A **React artifact** with interactive visualizations (see Artifact Spec below)
2. A brief **inline summary** in chat (3-5 bullet points max — the artifact is the primary deliverable)

---

#### 📊 Artifact Spec — Interactive Project Pulse Dashboard

Generate a self-contained React JSX artifact using Tailwind CSS and recharts. The artifact receives all collected data hardcoded as constants at the top.

**Visual sections to include:**

**1. Header Bar**
- Project title, client name, health badge (🟢 Healthy / 🟡 At Risk / 🔴 Blocked)
- Key stats inline: Total tasks · Open · Overdue · Due date

**2. Task Status Donut Chart** (recharts PieChart)
- Segments: Done (green), In Progress (blue), To Do (amber), Hold (gray), Client Review (purple), Unassigned (red)
- Center label: completion percentage

**3. Assignee Workload Bar Chart** (recharts BarChart)
- Horizontal bars per team member
- Stacked: Open tasks (blue) + Overdue tasks (red)
- Sorted by total open tasks descending

**4. Flags Panel — Needs Attention**
- Cards for each flagged task (overdue / unassigned / critical-no-date)
- Color-coded left border: red = overdue, orange = critical, yellow = unassigned
- Shows: task title, assignee, status, due date, comment snippet if available

**5. PM Recommendations**
- Numbered list of 2-4 actionable items
- Each with an icon (⚠️ 📅 👤 ✅) and concrete wording

**Design rules:**
- Dark theme: bg-gray-950 / bg-gray-900 cards
- Accent: teal/cyan (#06b6d4) for primary highlights
- Font: monospace for task IDs, sans-serif for labels
- Compact, dense layout — this is a PM tool, not a marketing page
- No purple gradients, no Inter font, no generic AI aesthetics
- Use `import { PieChart, Pie, Cell, BarChart, Bar, XAxis, YAxis, Tooltip, ResponsiveContainer, Legend } from 'recharts'`

**After the artifact**, output a 3-5 bullet inline summary:
```
**[PROJECT] Pulse — [DATE]**
• Health: [🟢/🟡/🔴] [reason]
• [N] open tasks — [N] overdue
• [Key flag or risk]
• [Top recommendation]
```

---

#### Inline-only fallback
If artifact rendering fails or the user requests text-only, output the full text card structure below:

---

## 🔍 Project Pulse — [PROJECT TITLE]
**Client:** [CLIENT NAME] · **Status:** [ACTIVE/ON HOLD/etc.] · **Owner:** [OWNER NAME]
**Start:** [DATE] · **Due:** [DATE or "Not set"] · **Hours quoted:** [N or "Not set"]

---

### 📊 Task Overview

| Status | Count |
|---|---|
| ✅ Completed | N |
| 🔵 In Progress | N |
| 🟡 To Do | N |
| 🔴 Overdue | N |
| ⬜ Unassigned | N |
| **Total** | **N** |

**Health:** [🟢 Healthy / 🟡 At Risk / 🔴 Blocked] — [1 sentence explaining why]

Health scoring rules:
- 🟢 Healthy: <10% overdue, no critical unassigned tasks
- 🟡 At Risk: 10–30% overdue OR critical tasks with no due date
- 🔴 Blocked: >30% overdue OR multiple critical/important tasks unassigned

---

### 👥 By Assignee

| Team Member | Open Tasks | Overdue | Critical |
|---|---|---|---|
| [NAME] | N | N | N |
| Unassigned | N | — | — |

---

### ⚡ Needs Attention

List only flagged tasks (overdue, critical no-due-date, unassigned critical). For each:

```
#### 🔴 [TASK TITLE]  (or 🟠 / 🟡 based on flag type)
**Assignee:** [NAME or "Unassigned"] · **Status:** [STATUS] · **Severity:** [LEVEL] · **Due:** [DATE or "Not set"]
```

If comment thread exists:
```
> 💬 **Thread:** [1-2 sentence summary]
> **Latest:** [Who said what]
> **➡ Action:** [What PM needs to do]
```

If no flagged tasks:
_✅ No critical items — project is on track._

---

### 📋 All Open Tasks

Group by assignee. For each assignee:

```
**[ASSIGNEE NAME]**
• [TASK TITLE] — [STATUS] · Due: [DATE or "—"] · [🔴 OVERDUE if applicable]
```

Limit to first 20 tasks if > 20. Add: _"+N more tasks not shown — ask to see all."_

---

### 💡 PM Recommendations

Based on the data, provide 2-4 concrete recommendations. Examples:
- "3 tasks are unassigned — recommend assigning before [DATE]"
- "Pravin has 8 open tasks, 3 overdue — consider redistributing"
- "No tasks have due dates — recommend adding dates to In Progress items"
- "Project is 80% complete — good candidate for a client update"

Keep recommendations specific and actionable — not generic advice.

---

_Pulse generated [DATE TIME IST] · Orbit live data_

---

## Error Handling
- Project not found → "⚠️ Couldn't find a project matching '[name]'. Try: [list closest matches if any]"
- Multiple matches → list them and ask user to pick
- `get_project_summary` fails → skip summary table, note it, continue with task list
- `list_task_comments` fails for a task → skip comments for that task silently
- Always deliver whatever data was collected — partial pulse beats nothing

---

## Required MCP Connections
- **Orbit:** `https://orbitmcp.whitelabeliq.net/mcp/orbit`

## Orbit Tool Reference
- `list_projects` — resolve project by name
- `get_client_details` — resolve client and their projects
- `get_project_summary` — task counts by status and assignee
- `get_project_task_list` — full task list with filters
- `list_task_comments` — comment thread for flagged tasks
