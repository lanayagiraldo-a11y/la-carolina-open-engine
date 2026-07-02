# Open Engine — Provisioning Guide

## Linear Configuration

### Team: Carolina AI

**ID:** `60f90892-5bb6-417b-842f-1a9060bb138e`

### Custom Workflow States

| Order | Name | Type | ID |
|-------|------|------|----|
| 1 | Standing | unstarted | `beb9b000-ad01-493d-bce6-32baef1ab0f8` |
| 2 | Agent Todo | unstarted | `4dfaaea9-da66-46a0-b28b-e2d89867d3ac` |
| 3 | Agent Working | started | `43da17a8-600f-4f8b-a0cc-054a4fda86cb` |
| 4 | Agent Needs Input | started | `8ccdd5c0-fde1-47f8-a519-56b3cb9df86b` |
| 5 | Agent Review | started | `830dc698-d93d-4287-b9aa-a113d599c694` |
| 6 | Agent Done | completed | `8061096a-54d4-4128-b7e4-cf5ac275fc4f` |

### Label

- **Name:** `agent-instructions`
- **Color:** `#E04F5F`
- **ID:** `3c088e83-c75f-42f4-bde1-c56ff8d5ab51`

### Project

- **Name:** Team Agent Engine
- **ID:** `28b90228-8efa-4a49-9b24-696fa0cd24fe`

### Standing Issues

| Issue | ID | Purpose |
|-------|----|---------|
| Status Ledger | `CAR-5` | AGENT STATUS comment per agent |
| Optional Standing Skills | `CAR-6` | Directory of installable skills |

## Issue Format

Agents only process issues with:
- **Title:** `[agent instructions][<agent-code>][task] <description>`
- **Label:** `agent-instructions`
- **Status:** Agent Todo

## Agent Codes

| Agent | Code | Runtime |
|-------|------|---------|
| Liliana's Hermes | `liliana-hermes` | Hermes Agent v0.18.0 |

## Private Context Template

Create a `~/.hermes/private-context.md` with:

```yaml
engine_version: open-engine:v0.1.0
agent_code: liliana-hermes
linear_team: Carolina AI
linear_team_id: 60f90892-5bb6-417b-842f-1a9060bb138e
linear_project: Team Agent Engine
linear_project_id: 28b90228-8efa-4a49-9b24-696fa0cd24fe
label: agent-instructions
label_id: 3c088e83-c75f-42f4-bde1-c56ff8d5ab51
status_ledger_id: CAR-5
optional_skills_dir_id: CAR-6
safety_boundaries:
  - Ask before deploy, publish, email, delete
  - Ask before changing billing/credentials
  - Customer-facing changes need approval
```
