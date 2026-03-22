<div align="center">

<br/>

<!-- WLIQ wordmark as text since GitHub doesn't render custom SVG inline -->
# WLIQ Skills

**Claude AI Skill Library · White Label IQ**

<br/>

[![Skills](https://img.shields.io/badge/skills-11-F37022?style=flat-square&labelColor=0e0e1a)](https://github.com/huebnermarketing/WLIQ-Skills/tree/main/skills)
[![Categories](https://img.shields.io/badge/categories-5-F37022?style=flat-square&labelColor=0e0e1a)](https://github.com/huebnermarketing/WLIQ-Skills/tree/main/skills)
[![MCP Integrations](https://img.shields.io/badge/MCP_integrations-6-F37022?style=flat-square&labelColor=0e0e1a)](https://github.com/huebnermarketing/WLIQ-Skills#mcp-requirements)
[![License](https://img.shields.io/badge/license-private-555?style=flat-square&labelColor=0e0e1a)](https://github.com/huebnermarketing/WLIQ-Skills)

<br/>

A growing library of specialised Claude skills for the White Label IQ team.
Connect Orbit, Notion, Gmail, Calendar, Fathom, and Pipedrive — drop in a `.skill` file and it just works.

<br/>

[**Browse Skills →**](https://github.com/huebnermarketing/WLIQ-Skills/tree/main/skills) &nbsp;·&nbsp; [**Installation Guide**](#installation) &nbsp;·&nbsp; [**Web Portal**](https://wliq-skills.tarangchokshi.workers.dev/)

<br/>

</div>

---

## Skill Library

### 🔑 Start Here — Master Skills

These two skills are the foundation. Install `wliq-master` first in every session.

| Skill | Trigger | What it does | MCPs |
|-------|---------|-------------|------|
| [`wliq-master`](skills/wliq-master.skill) | *any WLIQ query* | Identity resolution + routes every query to the right sub-skill (Orbit, Gmail, Calendar, Notion, Fathom, Pipedrive) | Orbit · Gmail · Calendar · Notion · Fathom · Pipedrive |
| [`orbit-skills`](skills/orbit-skills.skill) | *pulse on [project] / catch me up on [client]* | All Orbit workflows: client 360°, project health, team workload, weekly reports, billing analysis | Orbit · Notion |

**`wliq-master` includes 6 sub-skills:**
`wliq-orbit` · `wliq-gmail` · `wliq-calendar` · `wliq-notion` · `wliq-fathom` · `wliq-pipedrive`

**`orbit-skills` includes 5 sub-skills:**
`client-360` · `project-pulse` · `team-workload` · `weekly-report` · `adhoc-report`

---

### ⚡ WLIQ Core

| Skill | Trigger | What it does | MCPs |
|-------|---------|-------------|------|
| [`wliq-daily-briefing`](skills/wliq-daily-briefing.skill) | *today's agenda / morning briefing / what's my day* | PA-grade daily briefing — Orbit tasks due today, emails needing action, today's calendar. No emoji, no fluff. | Orbit · Gmail · Calendar |
| [`skills-discovery`](skills/skills-discovery.skill) | *find a skill for [task]* | Proactively searches claude-plugins.dev before Claude guesses. Ensures the right skill is always used. | None |

---

### 📄 Notion & Documents

| Skill | Trigger | What it does | MCPs |
|-------|---------|-------------|------|
| [`notion-to-pdf`](skills/notion-to-pdf.skill) | *make a PDF of this Notion page: [url]* | Converts any Notion page to a polished WLIQ-branded PDF. Always fetches live — never uses pasted content. | Notion |
| [`wiki-pdf-export`](skills/wiki-pdf-export.skill) | *export wiki to PDF* | Intercepts long/structured wiki responses and proactively offers to export as branded PDF. | Notion |

---

### 🔧 n8n Automation

| Skill | Trigger | What it does | MCPs |
|-------|---------|-------------|------|
| [`n8n-workflow-builder`](skills/n8n-workflow-builder.skill) | *build a workflow that does [X]* | Plain English → complete importable n8n JSON. Clarifying questions → visual flow summary → copy-paste JSON → node setup notes. | None |
| [`n8n-workflow-patterns`](skills/n8n-workflow-patterns.skill) | *what pattern for [workflow type]* | 5 battle-tested workflow architectures from real production setups: webhook, HTTP API, database, AI agent, scheduled. | None |

---

### 💻 Dev

| Skill | Trigger | What it does | MCPs |
|-------|---------|-------------|------|
| [`laravel`](skills/laravel.skill) | *help me build [feature] in Laravel* | Deep Laravel 12.x expertise based on 130 pages of official docs. Routing, Eloquent, queues, Vite. Built for WLIQ's SaaS Matrix stack. | None |

---

### 🎨 Brand

| Skill | Trigger | What it does | MCPs |
|-------|---------|-------------|------|
| [`wliq-brand`](skills/wliq-brand.skill) | *apply WLIQ branding* (explicit only) | Official WLIQ brand guidelines — black/white/orange palette, Avenir typography, logo rules, web, social, print, illustration. | None |
| [`collage-labs-color`](skills/collage-labs-color.skill) | *apply Collage branding* | Verified Collage Labs (collage.inc) color system + official logo SVGs. Primary: `#6473FF` indigo. Extracted March 2026. | None |

---

## Installation

Choose the method that matches how you use Claude.

### Claude Desktop (Cowork mode) — Recommended

> This is the primary method. Skills run automatically when Claude detects them in your workspace.

**Step 1** — Download the `.skill` file from the table above or the [skills folder](skills/).

**Step 2** — Open Claude Desktop and start a **Cowork** session. Select or create your workspace folder (e.g. `~/Documents/wliq-workspace`).

**Step 3** — Place the skill file in your workspace:

```bash
# Create the skills folder if it doesn't exist
mkdir -p ~/Documents/wliq-workspace/.skills/skills/orbit-skills

# Copy the skill file (rename to SKILL.md)
cp ~/Downloads/orbit-skills.skill \
   ~/Documents/wliq-workspace/.skills/skills/orbit-skills/SKILL.md
```

> Folder structure: `.skills/skills/<skill-name>/SKILL.md`

**Step 4** — Connect required MCPs (see [MCP Requirements](#mcp-requirements) below).

**Step 5** — Restart your Cowork session. Claude auto-detects the skill. Use any trigger phrase — no manual invocation needed.

---

### Claude.ai (Web — Projects)

> Skills work via the Projects feature on Claude.ai.

**Step 1** — Download the `.skill` file and open it in any text editor.

**Step 2** — Go to [claude.ai](https://claude.ai) → **Projects** → create or open a project.

**Step 3** — Open **Project Settings** → **Project Knowledge** → click **Add content**.

**Step 4** — Paste the skill file contents as a knowledge document. Name it the skill name (e.g. `wliq-master`).

**Step 5** — Start a conversation in that project — the skill activates automatically from project context.

---

### Claude Code / CLI

> Skills integrate with Claude Code via your project directory.

**Step 1** — Download the `.skill` file from the [skills folder](skills/).

**Step 2** — Place it in your project:

```bash
# Create the skills directory
mkdir -p .claude/skills/orbit-skills

# Copy and rename
cp ~/Downloads/orbit-skills.skill .claude/skills/orbit-skills/SKILL.md
```

**Step 3** — Reference the skill in your `CLAUDE.md`:

```markdown
## Active Skills

Read the following skill files before starting work:
- .claude/skills/orbit-skills/SKILL.md
- .claude/skills/wliq-master/SKILL.md
```

**Step 4** — Claude Code picks up the skills automatically when working in that directory.

---

### Manual (Any Claude Interface)

> Works with any Claude interface — Claude.ai, API, third-party apps.

**Step 1** — Download and open the `.skill` file in any text editor.

**Step 2** — Copy the full file contents.

**Step 3** — Paste into the **system prompt** or **custom instructions** field of your Claude interface.

**Step 4** — Start your conversation — the skill is active for that session.

---

## MCP Requirements

Some skills need MCP (Model Context Protocol) server connections to fetch live data. Skills will load without them but won't be able to call external tools.

| MCP | Required by | Setup |
|-----|-------------|-------|
| **Orbit** | `wliq-master`, `orbit-skills`, `wliq-daily-briefing` | Internal WLIQ tool — ask AI Matrix team |
| **Gmail** | `wliq-master`, `wliq-daily-briefing` | Google OAuth via Claude MCP settings |
| **Google Calendar** | `wliq-master`, `wliq-daily-briefing` | Google OAuth via Claude MCP settings |
| **Notion** | `wliq-master`, `notion-to-pdf`, `wiki-pdf-export` | Notion API token via Claude MCP settings |
| **Fathom** | `wliq-master` | Fathom API key via Claude MCP settings |
| **Pipedrive** | `wliq-master` | Pipedrive API token via Claude MCP settings |

For MCP setup help: [tarangc@whitelabeliq.com](mailto:tarangc@whitelabeliq.com)

---

## Repo Structure

```
WLIQ-Skills/
├── README.md
├── CONTRIBUTING.md
├── index.html                        ← Web portal (skills browser)
└── skills/
    ├── wliq-master.skill             ← Install first
    ├── orbit-skills.skill
    ├── wliq-daily-briefing.skill
    ├── skills-discovery.skill
    ├── notion-to-pdf.skill
    ├── wiki-pdf-export.skill
    ├── n8n-workflow-builder.skill
    ├── n8n-workflow-patterns.skill
    ├── laravel.skill
    ├── wliq-brand.skill
    └── collage-labs-color.skill
```

Each `.skill` file is a ZIP archive containing a `SKILL.md` and optional `references/` folder. Rename to `.md` to inspect contents directly.

---

## Troubleshooting

**Skill not triggering in Cowork?**
- Confirm the file is at `.skills/skills/<skill-name>/SKILL.md` (not nested deeper)
- Restart the Cowork session after installing
- Try explicitly saying the trigger phrase from the skill's card

**Skill triggers but fails mid-run?**
- Check the required MCPs are connected (see table above)
- Verify your Orbit/Notion/Gmail credentials are active

**Permission error on macOS?**
```bash
chmod -R 755 ~/Documents/wliq-workspace/.skills/
```

**Want to inspect a skill's contents?**
```bash
cp skills/orbit-skills.skill /tmp/orbit-skills.zip && unzip /tmp/orbit-skills.zip -d /tmp/orbit-skills
```

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add or update skills.

---

<div align="center">

Built by **[Tarang Chokshi](mailto:tarangc@whitelabeliq.com)** and the AI Matrix team · White Label IQ

</div>
