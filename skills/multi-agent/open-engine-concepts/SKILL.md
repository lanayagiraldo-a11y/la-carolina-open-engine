---
name: open-engine-concepts
description: "Teach multi-agent coordination via Linear queue."
version: 0.2.0
author: Hermes
metadata:
  hermes:
    tags: [MultiAgent, Linear, Queue, Coordination]
---

# Open Engine — Multi-Agent Coordination System

A shared operating surface for agents using Linear as the queue. Teaches the architecture: statuses, receipts, runner loop, standing context, and smoke tests. **Defaults to teaching, not configuring** — only set up when the user says "configura" or "monta".

Use this skill when the user asks about Open Engine, multi-agent queues, agent coordination, Linear-based agent workflows, or wants to understand the concept.

## Core Concepts

### 1. Linear as the Queue — Plain English

Linear is a task board like Trello or Asana but faster. Each task is an **issue**. Issues move through states (pending → in progress → done). 

Agents only touch issues marked with:
- **Title pattern**: `[agent instructions][<agent-code>][task] <outcome>`
- **Label**: `agent-instructions`
- **Status**: One of six (see below)

### 2. Six Workflow Statuses

| Status | Plain meaning |
|--------|--------------|
| **Standing** | Config permanente (skills, contactos, rutas) |
| **Agent Todo** | Tarea esperando que un agente la agarre |
| **Agent Working** | Un agente ya la tomó y está trabajando |
| **Agent Needs Input** | El agente está esperando que el humano le responda |
| **Agent Review** | Ya está lista, pero necesita que la revises |
| **Agent Done** | Completada ✅ |

### 3. The Runner Loop

Each run does this in order:

1. Identify runtime's agent code; open the status ledger; set Last queue result: checking
2. **Standing preflight** — compare local context versions vs Standing issues; apply updates
3. **Optional skill preflight** — update only already-subscribed optional skills; never browse new ones
4. **Check HUMAN HOLD** — if held issue shows AGENT HUMAN ANSWERED, resume and finish it
5. **Check BLOCKED** — if blocked issue has its answer, resume and finish it
6. **Check delegated issues** — note state changes (AGENT FOLLOW-UP)
7. **Claim oldest eligible Agent Todo** — move to Agent Working, leave AGENT CLAIMED
8. **Do the scoped work** — only what the issue says
9. **Finish** — AGENT DONE → Agent Done (or Agent Review if QA needed)
10. **Block or hold** — AGENT BLOCKED (Linear answer) or AGENT HUMAN HOLD (agent thread) → Agent Needs Input
11. **Fail** — AGENT FAILED only for unrecoverable errors
12. Update ledger, stop after exactly one task

### 4. Receipts (Exact Tokens)

Agents post these as comments on the issue:

| Token | Plain meaning |
|-------|--------------|
| `AGENT CLAIMED` | "Esta tarea es mía, la agarro" |
| `AGENT DONE` | "Terminé" |
| `AGENT BLOCKED` | "Necesito que respondas en el issue de Linear" |
| `AGENT UNBLOCKED` | "Llegó la respuesta, continúo" (justo antes de AGENT RESUMED) |
| `AGENT HUMAN HOLD` | "Necesito que me respondas AQUÍ en el chat" |
| `AGENT HUMAN ANSWERED` | "Gracias, ya respondiste en el chat" |
| `AGENT RESUMED` | "Sigo trabajando después de la pausa" |
| `AGENT FAILED` | "Algo salió mal" (solo para errores irrecuperables) |
| `AGENT APPLIED` | "Instalé/configuré una versión nueva" |
| `AGENT SKILL SUBSCRIBED` | "Aprobaste que instale un skill opcional" |
| `AGENT SKILL INSTALLED` | "Skill opcional instalado" |
| `AGENT SKILL UPDATED` | "Skill opcional actualizado" |
| `AGENT SKILL DECLINED` | "Rechazaste un skill opcional" |
| `AGENT FOLLOW-UP` | "Una tarea delegada cambió de estado" |
| `AGENT STATUS` | Un solo comentario que el agente actualiza en cada ejecución |

### 5. Status Ledger

One issue per agent. Each agent owns exactly ONE top-level comment updated in place every run. Formato:

```
AGENT STATUS
Agent: <agent-code>
Human/operator: <name>
Runtime: <Codex | Claude | other>
Automation: <automation name or manual>
Automation state: <installed | manual-required | blocked | paused>
Last heartbeat: <ISO8601>
Last queue result: <checking | none | claimed ISSUE-ID | completed ISSUE-ID | ...>
Last successful run: <ISO8601 or unknown>
Local context: <engine version>; <routing map version>
Optional skills: <none or skill-id@version subscribed>
Notes: <none or short blocker>
```

### 6. Private Context Packet

Per-runtime file containing:
- Engine version, agent code
- Linear team/project, label name
- Allowed local sources
- Status ledger issue ID (placeholder until created)
- Optional standing skill directory issue ID
- Subscribed optional skills
- Safety boundaries

### 7. Standing Skills

- **Mandatory**: Installed during setup; every run checks for same-scope updates
- **Optional**: Listed in a directory issue; NOT installed during setup. Human must explicitly ask agent to inspect or install. First approval subscribes that runtime to future same-scope updates. Any expanded capability or new authority needs fresh approval.

### 8. Safety Boundaries

Ask before: publishing, emailing, posting to Slack/public, deploying, deleting data, changing billing, changing credentials, or any customer-facing change. External/destructive actions need explicit issue-level approval.

### 9. Smoke Tests (Prove the Loop)

| Test | What it proves |
|------|----------------|
| **Basic** | Create `[agent instructions][<code>][task] Say hello from the queue`. Expect AGENT CLAIMED → AGENT DONE |
| **Blocked-resume** | Create task missing one fact. First run: AGENT BLOCKED. Answer on issue. Next run: AGENT UNBLOCKED → AGENT RESUMED → AGENT DONE |
| **Human-hold** | Ask agent to request local permission. Expect AGENT HUMAN HOLD (not BLOCKED), holding on ledger. After reply: AGENT HUMAN ANSWERED → completion |
| **Optional directory** | Ask agent what optional skills exist. Expect summary, no install until approval |

## Teaching Script — Plain Language

Use this order and these metaphors when explaining the concept to a non-technical user.

### Step 1: The Factory Metaphor
> "Open Engine es como una **fábrica con robots trabajadores**. Linear es la **pizarra** donde todos ven qué hay que hacer y quién lo está haciendo."

### Step 2: What Linear Is
> "Es un tablero de tareas como Trello. Cada tarjeta es un **issue** que se mueve por columnas. Los agentes pueden leer y escribir en Linear automáticamente."

### Step 3: The Queue (focus on 6 states)
> "Cada tarea pasa por 6 estados. Los más importantes para ti son:
> - **Agent Todo** → esperando que el agente la agarre
> - **Agent Working** → el agente la está haciendo
> - **Agent Needs Input** → el agente necesita que tú le respondas algo
> - **Agent Done** → terminada ✅"

### Step 4: The Receipts
> "Cuando un agente toma una tarea, deja un mensaje automático. Es como un **ticket de avance**: AGENT CLAIMED = 'yo la hago', AGENT DONE = 'terminé', AGENT BLOCKED = 'necesito un dato tuyo'."

### Step 5: The Runner Loop
> "Cada vez que el agente se activa (cada hora, o cuando tú lo llamas), hace esto en orden:
> 1. Revisa skills nuevos
> 2. Ve si una tarea que estaba esperando ya fue respondida
> 3. Toma la tarea más vieja de su cola
> 4. La ejecuta y deja receipt
> 5. Para después de UNA sola tarea"

### Step 6: The Ledger
> "Cada agente tiene un solo comentario que actualiza siempre. Ahí ves: su última actividad, si está instalado, si está funcionando."

### Step 7: Offer Setup
> Only switch to setup mode when the user says "configura", "monta", or "vamos".

## Prerequisites

- Linear workspace with MCP access
- Agent runtime with Linear MCP connected
- Six workflow statuses created on the Linear team

## Naming Conventions (concrete examples)

| Element | Example value |
|---------|--------------|
| Linear team name | `Carolina AI` |
| Project name | `Team Agent Engine` |
| Label | `agent-instructions` |
| Agent code | `liliana-hermes` |

## Setup: Adding Linear MCP to Hermes

For the `hermes-agent` runtime, add Linear via:

```bash
hermes mcp add linear --url https://mcp.linear.app/mcp
```

On a **headless server** (no browser), pipe answers because the connection check fails without auth:

```bash
printf 'Y\n\nY\n' | hermes mcp add linear --url https://mcp.linear.app/mcp
```

After adding, the user must authenticate from their local machine where a browser is available:

```bash
hermes mcp login linear
```

Verify with:

```bash
hermes mcp test linear
```

See `references/linear-mcp-headless-setup.md` for the full headless server setup transcript and quirks.
See `references/provisioning-graphql.md` for the complete setup commands (GraphQL mutations for statuses, labels, project, ledger, and standing issues).
See `references/mcp-runner-workflow.md` for the validated runner loop tool-call sequence (claim → work → done → ledger update) using Linear MCP tools.

## Pitfalls

- **Label spelling must be exact**: `agent-instructions` — runner filters on this
- **Title brackets**: `[agent instructions]` in title, second bracket is agent code
- **One task per run**: The runner stops after exactly one issue; no batching
- **Ledger comment**: Must be updated IN PLACE, never a fresh comment each run
- **Safe boundary**: The RUNNER prompt itself must never be edited to bypass safety
- **First approval**: Subscribes to future same-scope updates; new capability needs fresh approval
- **Blocked vs Hold**: BLOCKED = answer belongs on the Linear issue; HUMAN HOLD = answer belongs in the agent thread/app
- **Receipt order**: AGENT UNBLOCKED then AGENT RESUMED (not reversed)
- **`hermes config set` misplaces MCP entries**: `hermes config set mcp_servers.linear '{"url":...}'` places the entry at the config TOP LEVEL, not under `mcp_servers:`. Always use `hermes mcp add` to register MCP servers.
- **Headless OAuth**: Linear MCP needs browser-based OAuth. On a server without a display, use `printf` to pipe answers through `hermes mcp add`, then the user runs `hermes mcp login linear` from their local machine.
- **Disabled after headless add**: If saved without auth, the Linear MCP is `✗ disabled`. It becomes enabled only after successful `hermes mcp test linear` or `hermes mcp login linear`.
- **Teaching before configuring**: Default to concept explanation. Users who say "no lo configures, enséñame" are expressing a strong preference — switch to teaching mode immediately.
