# WLIQ Claude Skills

[![Visitors](https://visitor-badge.laobi.icu/badge?page_id=huebnermarketing.WLIQ-Skills)](https://github.com/huebnermarketing/WLIQ-Skills)

A shared library of Claude AI skills built by the White Label IQ team. These skills extend Claude's capabilities inside **Claude Desktop (Cowork mode)** with WLIQ-specific workflows — Orbit project management, Notion integrations, n8n automation, and brand-aware document generation.

---

## Skills Index

### Orbit (Project Management)
| Skill | What it does |
|-------|-------------|
| `orbit-client-360` | Full 360° client overview across all active projects — health, tasks, team, flags |
| `orbit-project-pulse` | Rich project health card for any named Orbit project or client |
| `orbit-team-workload` | Live workload + capacity view for any WLIQ matrix team |
| `orbit-weekly-report` | Comprehensive weekly report for any matrix team with per-member breakdown |

### WLIQ Core
| Skill | What it does |
|-------|-------------|
| `wliq-context` | Master context loader — team directory, Orbit IDs, client domains, tool routing |
| `wliq-daily-briefing` | Personalised daily briefing pulling Orbit tasks, Gmail, and Google Calendar |
| `wliq-brand` | Apply WLIQ brand guidelines to any document or artifact |

### Notion & Documents
| Skill | What it does |
|-------|-------------|
| `notion-to-pdf` | Convert any Notion page into a polished WLIQ-branded PDF |
| `wiki-pdf-export` | Export WLIQ Notion wiki content as a branded PDF |

### n8n Automation
| Skill | What it does |
|-------|-------------|
| `n8n-workflow-builder` | Build complete, importable n8n workflow JSON from plain-language descriptions |
| `n8n-workflow-patterns` | Proven architectural patterns for n8n workflows |

---

## Installation

### Prerequisites
- [Claude Desktop](https://claude.ai/download) installed
- Cowork mode enabled (or access to Claude Code with skills support)
- Git installed on your machine

### Step 1 — Clone this repo

```bash
git clone https://github.com/huebnermarketing/WLIQ-Skills.git
```

### Step 2 — Open Cowork and select your workspace folder

1. Open Claude Desktop
2. Start a Cowork session
3. When prompted to select a folder, **select or create a folder** (e.g. `~/Documents/wliq-workspace`)

This becomes your workspace root. Claude can read/write inside it.

### Step 3 — Copy skills into your workspace

Claude looks for skills in a `.skills/skills/` folder inside your workspace. Copy the skills you want from this repo into that location:

```bash
# From inside your cloned repo
cp -r skills/orbit-client-360  ~/Documents/wliq-workspace/.skills/skills/
cp -r skills/wliq-context       ~/Documents/wliq-workspace/.skills/skills/
cp -r skills/wliq-daily-briefing ~/Documents/wliq-workspace/.skills/skills/
# ... add whichever skills you need
```

Or to install ALL skills at once:

```bash
cp -r skills/* ~/Documents/wliq-workspace/.skills/skills/
```

> **Note:** If the `.skills/skills/` folder doesn't exist yet, create it first:
> ```bash
> mkdir -p ~/Documents/wliq-workspace/.skills/skills/
> ```

### Step 4 — Restart your Cowork session

Close and reopen Claude Desktop (or start a new Cowork session). Claude will automatically detect and load the skills from your workspace.

### Step 5 — Verify installation

In a Cowork session, type something like:

> *"catch me up on [client name]"*

Claude should trigger the `orbit-client-360` skill automatically. If it does, you're good to go.

---

## Updating Skills

When this repo gets new skills or updates:

```bash
cd wliq-skills
git pull
cp -r skills/* ~/Documents/wliq-workspace/.skills/skills/
```

Restart your Cowork session to pick up changes.

---

## MCP Connections Required

Some skills require MCP (Model Context Protocol) server connections to be set up in Claude Desktop. Without these, the skill will load but won't be able to fetch live data.

| Skill Group | Required MCPs |
|-------------|--------------|
| `orbit-*` | Orbit MCP (internal WLIQ tool) |
| `wliq-daily-briefing` | Orbit MCP, Gmail MCP, Google Calendar MCP |
| `notion-to-pdf`, `wiki-pdf-export` | Notion MCP |
| `n8n-*` | None (generates JSON output only) |
| `wliq-brand`, `collage-brand` | None |

Ask Tarang or the AI Matrix team for MCP setup instructions.

---

## Troubleshooting

**Skill not triggering?**
- Make sure the skill folder is directly inside `.skills/skills/` (not nested deeper)
- Each skill folder must contain a `SKILL.md` file
- Restart the Cowork session after installing

**Permission errors on macOS?**
```bash
chmod -R 755 ~/Documents/wliq-workspace/.skills/skills/
```

**Skill triggers but fails mid-run?**
- Check that the required MCPs are connected (see table above)
- Ensure your Orbit user ID, Notion token, etc. are correctly configured

---

## Repo Structure

```
wliq-skills/
├── README.md               ← You are here
├── CONTRIBUTING.md         ← How to add or update skills
├── .gitignore
└── skills/
    ├── orbit-client-360/
    │   └── SKILL.md
    ├── orbit-project-pulse/
    │   └── SKILL.md
    ├── orbit-team-workload/
    │   └── SKILL.md
    ├── orbit-weekly-report/
    │   └── SKILL.md
    ├── wliq-context/
    │   └── SKILL.md
    ├── wliq-daily-briefing/
    │   └── SKILL.md
    ├── wliq-brand/
    │   ├── SKILL.md
    │   └── references/     ← Brand assets (colors, typography, logo guides)
    ├── collage-brand/
    │   ├── SKILL.md
    │   └── references/
    ├── notion-to-pdf/
    │   ├── SKILL.md
    │   └── references/
    ├── wiki-pdf-export/
    │   ├── SKILL.md
    │   └── references/
    ├── n8n-workflow-builder/
    │   └── SKILL.md
    └── n8n-workflow-patterns/
        └── SKILL.md
```

---

## Team

Built and maintained by **Tarang Chokshi** (Director of Product Development, WLIQ) and the AI Matrix team.

For questions: tarangc@whitelabeliq.com
