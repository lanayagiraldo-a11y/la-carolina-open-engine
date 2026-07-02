# Open Engine Runner — MCP Workflow (Validated)

Secuencia de herramientas Linear MCP para ejecutar el runner loop en Hermes.

## 1. Claim & Start Working

```markdown
# Postear AGENT CLAIMED como comentario
mcp_linear_save_comment(
  issueId: "<ISSUE_ID>",
  body: "AGENT CLAIMED — Tomo esta tarea."
)

# Mover estado a Agent Working
mcp_linear_save_issue(
  id: "<ISSUE_ID>",
  state: "Agent Working",
  team: "Carolina AI"
)
```

## 2. Execute the Task

Hacer el trabajo real: consultar datos, analizar, generar archivos, etc.

## 3. Finish

```markdown
# Postear AGENT DONE
mcp_linear_save_comment(
  issueId: "<ISSUE_ID>",
  body: "AGENT DONE ✅ — [resultado del trabajo]"
)

# Mover estado a Agent Done (cierra el issue)
mcp_linear_save_issue(
  id: "<ISSUE_ID>",
  state: "Agent Done",
  team: "Carolina AI"
)
```

## 4. Update Status Ledger

Buscar el issue Standing "Status Ledger" filtrando por label `agent-instructions` y estado `Standing`:

```markdown
mcp_linear_list_issues(
  state: "Standing",
  team: "Carolina AI",
  query: "Status Ledger"
)
```

Leer los comentarios del Ledger para encontrar el AGENT STATUS existente:

```markdown
mcp_linear_list_comments(issueId: "<LEDGER_ISSUE_ID>")
```

Actualizar el comentario AGENT STATUS IN PLACE (usando su `id`):

```markdown
mcp_linear_save_comment(
  id: "<EXISTING_COMMENT_ID>",
  body: "AGENT STATUS\nAgent: liliana-hermes\n...\nLast queue result: completed <ISSUE_ID>\nLast successful run: <ISO8601>\n..."
)
```

## State Reference for Carolina AI

| State | MCP name | Type |
|-------|----------|------|
| Standing | "Standing" | unstarted |
| Agent Todo | "Agent Todo" | unstarted |
| Agent Working | "Agent Working" | started |
| Agent Needs Input | "Agent Needs Input" | started |
| Agent Review | "Agent Review" | started |
| Agent Done | "Agent Done" | completed |

## Label Reference

| Label | Note |
|-------|------|
| `agent-instructions` | Único label que filtra el runner |
