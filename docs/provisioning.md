# Open Engine — Provisioning Guide (Motor Liliana v1)

> 🔄 **Migración 2026-07-05:** configuración vigente = equipo **Motor (MOT)**.
> La configuración anterior (equipo Carolina AI, CAR-5/CAR-6/CAR-9) quedó archivada
> como referencia histórica al final de este documento.

## Linear Configuration

### Team: Motor

**ID:** `6ba90e34-8d5b-4580-be75-428f43e6047e`

### Custom Workflow States

| Order | Name | Type | ID |
|-------|------|------|----|
| 1 | Standing | unstarted | `44644161-c2de-4eb4-be50-e44e2970f9bf` |
| 2 | Agent Todo | unstarted | `8fe059bc-5ef0-420c-a968-3d8dcc68eb43` |
| 3 | Agent Working | started | `f31d680c-8a5b-47b9-a8b3-b2cedfd0d26c` |
| 4 | Agent Needs Input | started | `9e7e22f4-64db-4d98-bc3a-7f71089d4cbe` |
| 5 | Agent Review | started | `77059612-4a77-4807-8674-aaa7e4cf0808` |
| 6 | Agent Done | completed | `90a4b218-5cbb-4c54-8128-49f5a9061e47` |

Los estados default de Linear (Todo, In Progress, In Review, Done) fueron eliminados
del equipo — el tablero solo habla el idioma del motor.

### Label

- **Name:** `agent-instructions`
- **Color:** `#DCBE61` (dorado La Carolina)
- **ID:** `0f7f529a-3d6b-4170-85d6-5e5f3980b8e6`

### Projects (uno por tema)

| Project | ID | Tema |
|---------|----|----|
| La Carolina | `b80982cc-36da-4d30-b892-6d15886f0053` | MTC — transporte |
| (futuros) | — | IERA, El Salvador, Interstaff... |

### Standing Issues

| Issue | ID | Purpose |
|-------|----|---------|
| Core context v1 (reglas) | `MOT-2` (`4f3b9299-7860-4727-a06a-791226f6569a`) | Las 7 reglas permanentes + elegibilidad |
| Status Ledger | `MOT-1` (`4af83e6a-960d-40eb-9ffb-b4f102988581`) | AGENT STATUS comment per agent (in-place) |
| Optional Standing Skills | `MOT-8` | Directorio de skills instalables + credenciales |

## Issue Format (los 3 candados)

Agents only process issues with:
- **Title:** `[agent instructions][<agent-code>][task] <description>` — con el código de ESE agente
- **Label:** `agent-instructions`
- **Status:** Agent Todo (o retomada desde Agent Needs Input)

## Agent Codes

| Agent | Code | Runtime | Estado |
|-------|------|---------|--------|
| Claude de Liliana | `lili-claude` | Claude Code (Mac) | ✅ installed (2026-07-04) |
| Codex de Liliana | `lili-codex` | Codex CLI (Mac) | ✅ installed (2026-07-04) |
| Hermes de Liliana | `liliana-hermes` | Hermes Agent (VPS) | ✅ installed en Motor (2026-07-05) |

## Reglas destacadas (detalle completo en MOT-2)

- Una tarea por corrida; recibos en inglés mayúsculas; trabajo en español.
- Bóveda Obsidian "Liliana A": **solo lectura por defecto**. Escribir requiere
  propuesta en el issue + aprobación explícita de Liliana + recibo `AGENT VAULT WRITTEN`.
- Contexto Sharia/halal SOLO en contenido islámico o si Liliana lo pide en el issue.
- Archivos: el TEMA define la carpeta (MTC, IERA...); en duda → `~/AI/00_INBOX/`.
- Nunca publicar/enviar/desplegar/borrar sin aprobación explícita en el issue.

## Private Context Template

Create a `~/.hermes/private-context.md` (o equivalente del runtime) with:

```yaml
engine_version: motor-liliana:v1
agent_code: <código del agente>
linear_team: Motor
linear_team_id: 6ba90e34-8d5b-4580-be75-428f43e6047e
label: agent-instructions
label_id: 0f7f529a-3d6b-4170-85d6-5e5f3980b8e6
core_context_id: MOT-2
status_ledger_id: MOT-1
optional_skills_dir_id: MOT-8
safety_boundaries:
  - Ask before deploy, publish, email, delete
  - Ask before changing billing/credentials
  - Customer-facing changes need approval
  - Obsidian vault read-only; writes need approval + AGENT VAULT WRITTEN receipt
```

Para agentes del Mac de Liliana, el protocolo operativo completo del queue runner
vive en `~/AI/open-engine/queue-runner.md`.

---

## 📦 Configuración histórica (equipo Carolina AI — archivado 2026-07-05)

Primera iteración del motor (2026-07-02, solo liliana-hermes). Conservada para
trazabilidad: team `Carolina AI` id `60f90892-5bb6-417b-842f-1a9060bb138e`,
ledger CAR-5, skills CAR-6, reglas CAR-9, label id `3c088e83-c75f-42f4-bde1-c56ff8d5ab51`,
project Team Agent Engine `28b90228-8efa-4a49-9b24-696fa0cd24fe`.
