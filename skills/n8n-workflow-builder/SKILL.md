---
name: n8n-workflow-builder
description: "Build complete, importable n8n workflow JSON from a plain-language description. Use this skill whenever a user asks to build, create, generate, or design an n8n workflow — even if they just describe what they want automated without saying 'n8n workflow JSON'. Also trigger when a user says things like 'I want to automate X in n8n', 'help me build a workflow that does Y', 'can you create an n8n flow for Z', or 'I need a webhook that triggers X'. Always ask clarifying questions first, then produce a visual flow summary, raw copy-paste JSON, node-by-node explanation, and credential/setup notes."
---

# n8n Workflow Builder

Generates complete, importable n8n workflow JSON from a user's plain-language description.

---

## Your Job

Walk the user through a structured intake, then produce four outputs:
1. **Visual flow summary** — ASCII diagram showing nodes and connections
2. **Importable JSON** — valid n8n workflow JSON in a code block, ready to paste
3. **Node-by-node explanation** — plain English for each node
4. **Setup notes** — credentials, config placeholders, and what to change after import

---

## Step 1 — Clarifying Questions

Before writing any JSON, ask ALL of the following in a single message. Group them cleanly:

### Always ask:
1. **Trigger** — What starts this workflow? (webhook, schedule, manual click, email received, form submission, etc.)
2. **Goal** — What should the workflow ultimately do? (send a message, update a database, call an API, etc.)
3. **Services/Apps** — Which apps or APIs are involved? (Slack, Gmail, Postgres, OpenAI, Airtable, HTTP endpoints, etc.)
4. **Conditions** — Any branching logic? (e.g. "only send email if status = approved")
5. **Error handling** — Should failures trigger a notification or just silently fail?

### Ask only if relevant:
- **Schedule** (if cron trigger): frequency? timezone?
- **AI involvement**: which model? what should it do?
- **Data shape**: any specific fields, payload structure, or field mappings to know about?
- **Volume**: is this processing one item or bulk/batches?

Once you have answers, confirm back to the user: *"Here's what I'm going to build: [one-paragraph summary]. Ready to generate?"* — then proceed.

---

## Step 2 — Produce the Four Outputs

### Output 1: Visual Flow Summary

Use an ASCII diagram before the JSON. Example:

```
[Webhook] → [Set: Map Fields] → [IF: Status = approved?]
                                      ↓ YES          ↓ NO
                               [Slack: Notify]  [Stop & Error]
```

Label each node with its type and purpose. Show branching where applicable.

---

### Output 2: Importable JSON

Generate valid n8n workflow JSON. Follow the structure below precisely.

#### Canonical Workflow JSON Structure

```json
{
  "name": "Workflow Name",
  "nodes": [...],
  "connections": {...},
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "1",
  "meta": {
    "instanceId": "generated-workflow"
  }
}
```

#### Node Object Structure

Every node must have:
```json
{
  "id": "unique-uuid-string",
  "name": "Human Readable Name",
  "type": "n8n-nodes-base.nodeType",
  "typeVersion": 1,
  "position": [x, y],
  "parameters": {}
}
```

- **id**: Use simple unique strings like `"node-1"`, `"node-2"` etc. (UUIDs work too)
- **position**: Space nodes 250px apart horizontally. Start at `[250, 300]`
- **typeVersion**: Use `1` unless you know a specific version is required

#### Connections Object Structure

```json
"connections": {
  "Node Name": {
    "main": [
      [{ "node": "Next Node Name", "type": "main", "index": 0 }]
    ]
  }
}
```

- Key is the **exact node name** (must match `"name"` field in the node object)
- `"main"` is an array of output arrays (one per output port)
- For IF nodes: index 0 = true branch, index 1 = false branch
- For nodes with no output (end nodes): omit from connections

---

### Common Node Type Reference

| Purpose | `type` value | Notes |
|---|---|---|
| Webhook | `n8n-nodes-base.webhook` | Add `httpMethod`, `path`, `responseMode` |
| Schedule | `n8n-nodes-base.scheduleTrigger` | Use `rule` parameter with cron |
| Manual Trigger | `n8n-nodes-base.manualTrigger` | No parameters needed |
| HTTP Request | `n8n-nodes-base.httpRequest` | Set `method`, `url`, `authentication` |
| Set (map fields) | `n8n-nodes-base.set` | Use `assignments` array |
| IF (condition) | `n8n-nodes-base.if` | Set `conditions` with `leftValue`, `operation`, `rightValue` |
| Switch | `n8n-nodes-base.switch` | Multiple output branches |
| Code (JS) | `n8n-nodes-base.code` | `jsCode` parameter |
| Send Email | `n8n-nodes-base.emailSend` | Requires SMTP credential |
| Gmail | `n8n-nodes-base.gmail` | Requires Gmail OAuth credential |
| Slack | `n8n-nodes-base.slack` | Requires Slack credential |
| Postgres | `n8n-nodes-base.postgres` | Requires Postgres credential |
| MySQL | `n8n-nodes-base.mySql` | Requires MySQL credential |
| Airtable | `n8n-nodes-base.airtable` | Requires Airtable credential |
| Google Sheets | `n8n-nodes-base.googleSheets` | Requires Google Sheets credential |
| OpenAI | `n8n-nodes-base.openAi` | Requires OpenAI credential |
| AI Agent | `@n8n/n8n-nodes-langchain.agent` | Sub-nodes required (model, memory, tools) |
| Webhook Response | `n8n-nodes-base.respondToWebhook` | Use after webhook trigger |
| Stop and Error | `n8n-nodes-base.stopAndError` | `errorMessage` parameter |
| Merge | `n8n-nodes-base.merge` | Combine two branches |
| Split in Batches | `n8n-nodes-base.splitInBatches` | `batchSize` parameter |
| Wait | `n8n-nodes-base.wait` | `amount` + `unit` parameters |
| Notion | `n8n-nodes-base.notion` | Requires Notion credential |

---

### Credential Placeholder Convention

Never hardcode credentials. Use this pattern in node parameters:

```json
"credentials": {
  "slackApi": {
    "id": "REPLACE_WITH_CREDENTIAL_ID",
    "name": "Slack account"
  }
}
```

The user will remap credentials in the n8n editor after import.

---

### Full Working Example — Webhook to Slack Notification

```json
{
  "name": "Webhook to Slack",
  "nodes": [
    {
      "id": "node-1",
      "name": "Webhook",
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 1,
      "position": [250, 300],
      "parameters": {
        "httpMethod": "POST",
        "path": "notify",
        "responseMode": "onReceived",
        "responseData": "firstEntryJson"
      }
    },
    {
      "id": "node-2",
      "name": "Send Slack Message",
      "type": "n8n-nodes-base.slack",
      "typeVersion": 2,
      "position": [500, 300],
      "parameters": {
        "operation": "postMessage",
        "channel": "#general",
        "text": "={{ $json.body.message }}"
      },
      "credentials": {
        "slackApi": {
          "id": "REPLACE_WITH_CREDENTIAL_ID",
          "name": "Slack account"
        }
      }
    }
  ],
  "connections": {
    "Webhook": {
      "main": [
        [{ "node": "Send Slack Message", "type": "main", "index": 0 }]
      ]
    }
  },
  "active": false,
  "settings": { "executionOrder": "v1" },
  "versionId": "1",
  "meta": { "instanceId": "generated-workflow" }
}
```

---

### Output 3: Node-by-Node Explanation

After the JSON, explain each node in plain English:

```
1. Webhook — Listens for POST requests at /webhook/notify. 
   Any system can hit this URL to trigger the workflow.

2. Send Slack Message — Posts the incoming "message" field 
   to the #general Slack channel.
```

---

### Output 4: Setup Notes

Always include this section after the explanation:

```
⚙️ Setup Checklist (after import)

Credentials to configure:
  □ [Node Name] — needs [Credential Type] (e.g. Slack OAuth, Postgres connection)

Fields to update:
  □ [Node Name] → [parameter] — currently set to placeholder "[value]"

Test approach:
  □ Activate workflow in test mode
  □ Trigger with: [example curl / test data / manual click]
  □ Expected result: [what success looks like]
```

---

## Step 3 — Iteration

After delivering all four outputs, ask:
> "Want to adjust anything — add a branch, change a node, add error handling, or modify the logic?"

Apply changes and regenerate only the affected JSON sections (or the full workflow if structure changed significantly). Always re-deliver the full JSON block so it remains copy-paste ready.

---

## Key Rules

- **Never output partial JSON** — always a complete, importable workflow
- **Always use `"active": false`** — user activates manually after testing
- **Always use `"executionOrder": "v1"`** — compatible with both cloud and self-hosted
- **Node names in connections must exactly match node `"name"` fields**
- **Never hardcode API keys, tokens, or passwords** in parameters
- **Webhook data is accessed via `$json.body.field`** not `$json.field`
- **For AI Agent nodes**, include sub-nodes (language model, memory) as separate node objects connected via `ai_languageModel` and `ai_memory` output types
- **Position nodes cleanly**: 250px horizontal spacing, branch vertically by 150px

---

## Compatibility Notes

These patterns work on both **n8n Cloud** and **self-hosted** (v1.0+):
- Use `typeVersion: 1` for most nodes unless you specifically need v2 features
- Slack node `typeVersion: 2` is required for `postMessage` operation
- Gmail node `typeVersion: 2` is required for most operations
- AI/LangChain nodes use `@n8n/n8n-nodes-langchain.*` prefix
- Avoid `n8n-nodes-base.function` (deprecated) — use `n8n-nodes-base.code` instead
