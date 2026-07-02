# Open Engine Provisioning via Linear GraphQL API

Full set of GraphQL mutations and queries to create the Open Engine structure programmatically (without the MCP tools, before they're loaded).

## Prerequisites

- Linear API key with admin or `All members` permissions for API key creation
- Team already created in Linear (e.g. "Carolina AI")
- Team ID obtained from: `{teams{nodes{id name}}}`

## 1. Get Team ID

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: Bearer <API_KEY>" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ teams { nodes { id name } } }"}' | python3 -m json.tool
```

## 2. Create Workflow Statuses

Create them in order. Each needs: name, color, type (unstarted | started | completed | canceled | duplicate), teamId.

Note: Does NOT support `position` parameter — ordering is manual in the Linear UI.

```bash
# Standing (backlog/unstarted — durable context)
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: Bearer <KEY>" \
  -H "Content-Type: application/json" \
  -d '{"query": "mutation { workflowStateCreate(input: {name: \"Standing\", color: \"#5E6AD2\", type: \"unstarted\", teamId: \"<TEAM_ID>\"}) { success workflowState { id name } } }"}'

# Agent Todo
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: Bearer <KEY>" \
  -H "Content-Type: application/json" \
  -d '{"query": "mutation { workflowStateCreate(input: {name: \"Agent Todo\", color: \"#f2c94c\", type: \"unstarted\", teamId: \"<TEAM_ID>\"}) { success workflowState { id name } } }"}'

# Agent Working (started — active)
curl -s -X POST ... -d '{"query": "mutation { workflowStateCreate(input: {name: \"Agent Working\", color: \"#E59700\", type: \"started\", teamId: \"<TEAM_ID>\"}) { success workflowState { id name } } }"}'

# Agent Needs Input (started — paused waiting human)
curl -s -X POST ... -d '{"query": "mutation { workflowStateCreate(input: {name: \"Agent Needs Input\", color: \"#E04F5F\", type: \"started\", teamId: \"<TEAM_ID>\"}) { success workflowState { id name } } }"}'

# Agent Review (started — wait for QA/approval)
curl -s -X POST ... -d '{"query": "mutation { workflowStateCreate(input: {name: \"Agent Review\", color: \"#0F783C\", type: \"started\", teamId: \"<TEAM_ID>\"}) { success workflowState { id name } } }"}'

# Agent Done (completed — closed)
curl -s -X POST ... -d '{"query": "mutation { workflowStateCreate(input: {name: \"Agent Done\", color: \"#5E6AD2\", type: \"completed\", teamId: \"<TEAM_ID>\"}) { success workflowState { id name } } }"}'
```

## 3. Create Label

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: Bearer <KEY>" \
  -H "Content-Type: application/json" \
  -d '{"query": "mutation { issueLabelCreate(input: {name: \"agent-instructions\", color: \"#E04F5F\", teamId: \"<TEAM_ID>\"}) { success issueLabel { id name } } }"}'
```

## 4. Create Project

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: Bearer <KEY>" \
  -H "Content-Type: application/json" \
  -d '{"query": "mutation { projectCreate(input: {name: \"Team Agent Engine\", teamIds: [\"<TEAM_ID>\"], description: \"Open Engine: multi-agent coordination queue\"}) { success project { id name } } }"}'
```

## 5. Create Status Ledger (Standing issue)

Uses Python for multiline body escaping:

```python
import json, requests

body = '''AGENT STATUS
Agent: <agent-code>
Human/operator: <name>
Runtime: Hermes Agent (server)
Automation: manual
Automation state: installed
Last heartbeat: <ISO8601>
Last queue result: none
Last successful run: <ISO8601>
Local context: open-engine:v0.1.0
Optional skills: none
Notes: <none or short>'''

mutation = 'mutation { issueCreate(input: {title: "[agent instructions][all agents][standing] Status Ledger", teamId: "<TEAM_ID>", projectId: "<PROJECT_ID>", labelIds: ["<LABEL_ID>"], stateId: "<STANDING_STATE_ID>", description: "%s"}) { success issue { id title } } }' % body.replace('\n', '\\n').replace('"', '\\"')

r = requests.post('https://api.linear.app/graphql',
    json={'query': mutation},
    headers={'Authorization': 'Bearer <KEY>', 'Content-Type': 'application/json'})
print(r.json())
```

## 6. Add AGENT STATUS Comment

```python
mutation = 'mutation { commentCreate(input: {issueId: "<LEDGER_ISSUE_ID>", body: "%s"}) { success comment { id } } }' % body.replace('\n', '\\n').replace('"', '\\"')

r = requests.post('https://api.linear.app/graphql',
    json={'query': mutation},
    headers={'Authorization': 'Bearer <KEY>', 'Content-Type': 'application/json'})
```

Note: Linear API does NOT support `commentUpdate`. To update a ledger comment, `commentDelete` it first, then `commentCreate` a new one.

## 7. Create Optional Standing Skills Directory

Same pattern as the Status Ledger but with title `[agent instructions][all agents][standing] Optional Standing Skills`.

## 8. Security: Restrict API Key Creation

If the workspace setting "Who can create API keys" is set to "Only admins", an admin must either:
- Change it to "All members" (in workspace Settings → Security → API)
- Or create the key for the user

## State ID Lookup

To find workflow state IDs after creation:
```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Authorization: Bearer <KEY>" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ workflowStates { nodes { id name } } }"}' | python3 -c "import sys,json; d=json.load(sys.stdin); [print(f\"{s['name']}: {s['id']}\") for s in d['data']['workflowStates']['nodes']]"
```

## Moving issue to Standing state

```python
mutation = 'mutation { issueUpdate(id: "<ISSUE_ID>", input: {stateId: "<STANDING_STATE_ID>"}) { success } }'
```

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| `401 Unauthorized` | API key invalid or expired | Generate new key at `https://linear.app/settings/security` |
| `Cannot query field` on mutation | Wrong field name or missing permission | Check Linear API schema docs |
| Unclosed string error | Multiline body in shell | Use Python requests with json=payload instead of shell escaping |
| `'str' object has no attribute 'get'` in `hermes mcp catalog` | Config has a string entry where dict expected (caused by `hermes config set`) | Fix the config with Python yaml |
