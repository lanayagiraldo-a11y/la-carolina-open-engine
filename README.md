# 🧠 La Carolina — Open Engine Multi-Agente

Sistema **Open Engine** multi-agente para **Metropolitana de Transportes La Carolina (MTC)**.  
Usa **Linear como cola de trabajo** para que agentes IA coordinados procesen tareas operativas, financieras, de datos y contenido.

---

## 🏗️ Arquitectura

> 🔄 **Migración 2026-07-05:** el motor vive ahora en el equipo **Motor (MOT)** de Linear
> — reconstruido desde cero por Liliana. El equipo Carolina AI (CAR) quedó archivado
> como referencia histórica.

```
                    ┌──────────────────────────┐
                    │   STATUS LEDGER (MOT-1)   │
                    │  Un comentario por agente │
                    │  Reglas del motor: MOT-2  │
                    └─────────┬────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
   ┌────▼─────┐        ┌─────▼─────┐         ┌─────▼──────┐
   │lili-claude│       │ lili-codex│         │  liliana-  │
   │Claude Code│       │ Codex CLI │         │  hermes    │
   │(Mac)      │       │ (Mac)     │         │  (VPS)     │
   └────┬─────┘        └─────┬─────┘         └─────┬──────┘
        │                    │                     │
   ┌────▼────────────────────▼─────────────────────▼────┐
   │               LINEAR QUEUE (equipo Motor)           │
   │  Standing → Agent Todo → Agent Working → Agent Done│
   │       ↕ Agent Needs Input ↕ Agent Review ↕          │
   │      Projects por tema: La Carolina · (IERA...)     │
   └─────────────────────┬──────────────────────────────┘
                         │
        ┌────────────────▼────────────────┐
        │         FUENTES DE DATOS        │
        │  Supabase │ GESTIVO │ Obsidian  │
        │  OneDrive │ GDrive  │ GEMA MySQL│
        └─────────────────────────────────┘
```

---

## 📋 Estados del Workflow

| Estado | Significado |
|--------|-------------|
| **Standing** | Config permanente (skills, contactos, rutas) |
| **Agent Todo** | Tarea esperando que un agente la agarre |
| **Agent Working** | Un agente la tomó y está trabajando |
| **Agent Needs Input** | El agente espera respuesta humana |
| **Agent Review** | Lista, necesita revisión |
| **Agent Done** | Completada ✅ |

---

## 🤖 Skills de La Carolina

| Skill | Propósito |
|-------|-----------|
| **🧠 Cerebro de Fuentes** | Mapa completo de fuentes de datos de LC |
| **📊 Data Intelligence** | Consultas SQL + dashboards Chart.js |
| **📈 Data** | Consultas operativas (Supabase, GESTIVO, GEMA) |
| **🏦 Agentes Financieros** | Multi-agente financiero (junta, contabilidad) |
| **🚌 Reporte de Bus** | Reporte 1 página de bus/conductor |
| **👥 Afiliados MTC** | Reportes individuales de buses afiliados |
| **📱 Content Studio** | 8 agentes para contenido y redes sociales |
| **🎨 Designer** | Material visual con branding oficial |
| **📋 WhatsApp Analysis** | Análisis de novedades operativas WhatsApp |
| **🕌 IERA Cerebro** | Cerebro de fuentes de IERA |

---

## 🏭 Open Engine (Coordinación Multi-Agente)

### Receipts (mensajes que deja el agente)

| Token | Significado |
|-------|-------------|
| `AGENT CLAIMED` | "Tomo esta tarea" |
| `AGENT DONE` | "Terminé ✅" |
| `AGENT BLOCKED` | "Necesito respuesta en Linear" |
| `AGENT HUMAN HOLD` | "Necesito respuesta en el chat" |
| `AGENT HUMAN ANSWERED` | "Gracias, ya respondiste" |
| `AGENT UNBLOCKED` | "Llegó la respuesta, continúo" |
| `AGENT RESUMED` | "Sigo trabajando" |
| `AGENT FAILED` | "Error irrecuperable" |
| `AGENT STATUS` | Bitácora actualizada del agente |
| `AGENT AUTOMATION READY` | "Instalado y smoke test superado" (firma en MOT-2) |
| `AGENT VAULT WRITTEN` | "Escribí en la bóveda Obsidian con aprobación previa" |

### Ciclo por ejecución

Cada vez que el agente se activa:

1. **Standing preflight** — verifica skills actualizados
2. **Check Human Hold** — retoma tarea si respondiste
3. **Check Blocked** — retoma si hay respuesta en Linear
4. **Claim** — toma la tarea más antigua de la cola
5. **Work** — ejecuta
6. **Finish** — AGENT DONE
7. **Para** después de exactamente una tarea

---

## 🎨 Branding Oficial

| Elemento | Valor |
|----------|-------|
| **Dorado** | `#DCBE61` |
| **Rojo** | `#C22219` |
| **Negro** | `#222222` |
| **Títulos** | Bebas Neue |
| **Cuerpo** | Montserrat |
| **Tagline** | "Transporte con corazón" |

---

## 🗄️ Fuentes de Datos

| Fuente | Contenido | Estado |
|--------|-----------|--------|
| **Supabase #1** | 35 tablas, ~128K registros (viajes, conductores, cierres, ausentismo) | ✅ |
| **GESTIVO API** | 19 recursos (rotación, accidentes, campañas Meta Ads) | ✅ |
| **Obsidian Vault** | Notas, análisis, reportes | ✅ |
| **OneDrive Corp** | Brand manual, brief estratégico, logos | ✅ |
| **Google Drive** | Archivos históricos La Carolina | ✅ |
| **GEMA MySQL** | Histórico 2025 | ❌ PWD expirado |

---

## 👥 Equipo Clave

| Persona | Rol |
|---------|-----|
| John Guerrero | Gerente Operativo y General |
| Edith Royero | Gerente Financiera |
| Helmuth | Jefe de Operaciones |
| Eduardo Luis | Jefe de Mantenimiento |
| Jennifer Pinzón | Directora GH |
| Néstor Molina | Analista de datos |
| Víctor Sandoval | Asesor externo IA |

---

## 🔗 Links

- **Linear Team:** Motor (MOT) — el equipo Carolina AI (CAR) quedó archivado
- **Linear Project:** La Carolina (los temas nuevos tendrán su propio project: IERA, El Salvador...)
- **Standing issues:** MOT-1 (Status Ledger) · MOT-2 (reglas del motor) · MOT-8 (skills opcionales)
- **Label:** `agent-instructions` (dorado #DCBE61)
- **Cerebro de fuentes:** github.com/lanayagiraldo-a11y/cerebro-carolina
- **Dashboard Unificado:** `dashboard-unificado-la-carolina.netlify.app`
- **Seguimiento Conductores:** `follow-up-on-driver-rotation-xl2t.vercel.app`
- **Afiliados:** `carolina-afiliados-x7k4q.netlify.app`

---

## 🧪 Smoke Test

```markdown
Crea un issue en el equipo Motor con:
- Título: `[agent instructions][<código-agente>][task] Tarea de prueba`
  (códigos activos: lili-claude · lili-codex · liliana-hermes)
- Estado: Agent Todo
- Label: agent-instructions

El agente lo toma en su próximo chequeo de cola. Protocolo completo del runner:
~/AI/open-engine/queue-runner.md (agentes del Mac) o MOT-2 (reglas para todos).
```

---

*"Transporte con corazón" ❤️ — Metropolitana de Transportes La Carolina*
