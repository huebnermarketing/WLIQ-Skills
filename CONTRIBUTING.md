# Contributing to WLIQ Skills

This guide explains how to add a new skill, update an existing one, and keep the repo clean.

---

## What is a Skill?

A skill is a folder containing a `SKILL.md` file. The `SKILL.md` has a YAML frontmatter block at the top followed by a markdown body with instructions for Claude.

```
skills/
└── my-new-skill/
    ├── SKILL.md          ← Required
    └── references/       ← Optional: supporting context files
        └── some-ref.md
```

### SKILL.md structure

```markdown
---
name: my-skill-name
description: >
  One or two sentences describing what this skill does and when Claude should
  trigger it. Include example trigger phrases here.
---

# My Skill Name

[Instructions for Claude go here]

## Step 1 — ...
## Step 2 — ...
```

The `description` field in the frontmatter is critical — Claude uses it to decide **when** to activate the skill. Write it clearly with example trigger phrases.

---

## Adding a New Skill

### 1. Create the skill folder

```bash
mkdir skills/your-skill-name
```

Folder naming convention: lowercase, hyphen-separated (e.g. `orbit-sprint-summary`, `gmail-daily-digest`).

### 2. Write the SKILL.md

Start with the frontmatter:

```yaml
---
name: your-skill-name
description: >
  What this skill does in 1-2 sentences. Include trigger phrases like
  "generate X", "show me Y", "create a Z report".
---
```

Then write the body: what Claude should do step by step, what data to fetch, how to present the output.

**Tips for a good skill:**
- Be explicit about which MCP tools to call (e.g. `get_project_details`, `gmail_search_messages`)
- Define the expected output format (table, HTML dashboard, inline text, etc.)
- List fallback behaviour if an MCP call fails
- Add example trigger phrases in the description

### 3. Add reference files (optional)

If your skill needs supporting context (brand guidelines, templates, config), put them in a `references/` subfolder:

```bash
mkdir skills/your-skill-name/references
# Add .md files with supporting content
```

### 4. Test locally

- Copy your skill folder into your local workspace: `~/.../wliq-workspace/.skills/skills/your-skill-name/`
- Restart Cowork
- Trigger the skill with a test prompt and verify the output

### 5. Commit and push

```bash
git checkout -b skill/your-skill-name
git add skills/your-skill-name/
git commit -m "skill: add your-skill-name"
git push origin skill/your-skill-name
```

Open a PR against `main` and add a short description of what the skill does and what MCPs it requires.

---

## Updating an Existing Skill

1. Edit the relevant `SKILL.md` (and any `references/*.md` files)
2. Test locally
3. Commit with a clear message:

```bash
git commit -m "skill(orbit-client-360): improve task overdue detection logic"
```

---

## Commit Message Convention

```
skill: add <skill-name>                    → new skill
skill(<skill-name>): <what changed>        → update to existing skill
fix(<skill-name>): <what was broken>       → bug fix
docs: <what changed>                       → README or CONTRIBUTING updates
```

---

## Skill Categories

Place new skills in the appropriate category for the README table:

| Category | Naming prefix | Description |
|----------|--------------|-------------|
| Orbit | `orbit-` | Anything pulling from Orbit MCP |
| WLIQ Core | `wliq-` | Internal WLIQ team workflows |
| Client Brand | `<client>-brand` | Client-specific brand guidelines |
| Notion | `notion-` | Notion read/export workflows |
| n8n | `n8n-` | n8n workflow generation |
| Documents | (no prefix) | Generic doc/file tools |

---

## Required MCPs Checklist

When adding a new skill, document the MCPs it depends on in this table (update `README.md`):

| MCP | Purpose | How to connect |
|-----|---------|---------------|
| Orbit MCP | Project/task data | Ask AI Matrix team |
| Notion MCP | Wiki + page content | Notion integration settings |
| Gmail MCP | Email access | Google OAuth |
| Google Calendar MCP | Calendar + meetings | Google OAuth |

---

## Questions?

Reach out to **Tarang Chokshi** (tarangc@whitelabeliq.com) or post in the AI Matrix team channel.
