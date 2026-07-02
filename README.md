# 🧠 La Carolina — Open Engine Multi-Agente

Sistema **Open Engine** multi-agente para **Metropolitana de Transportes La Carolina (MTC)**.  
Usa **Linear como cola de trabajo** para que agentes IA coordinados procesen tareas operativas, financieras, de datos y contenido.

---

## 🏗️ Arquitectura

```
                    ┌─────────────────────────┐
                    │    STATUS LEDGER (CAR-5) │
                    │  Un comentario por agente │
                    └─────────┬───────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
   ┌────▼────┐         ┌─────▼─────┐         ┌─────▼────┐
   │Agente   │         │ Agente    │         │ Agente   │
   │Hermes   │         │ (futuro)  │         │ (futuro) │
   └────┬────┘         └─────┬─────┘         └─────┬────┘
        │                    │                     │
   ┌────▼────────────────────▼─────────────────────▼────┐
   │              LINEAR QUEUE (Carolina AI)             │
   │  Standing → Agent Todo → Agent Working → Agent Done│
   │            ↕ Agent Needs Input ↕                    │
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

- **Linear Team:** Carolina AI
- **Linear Project:** Team Agent Engine
- **Label:** `agent-instructions`
- **Dashboard Unificado:** `dashboard-unificado-la-carolina.netlify.app`
- **Seguimiento Conductores:** `follow-up-on-driver-rotation-xl2t.vercel.app`
- **Afiliados:** `carolina-afiliados-x7k4q.netlify.app`

---

## 🧪 Smoke Test

```markdown
Crea un issue en Linear con:
- Título: `[agent instructions][liliana-hermes][task] Tarea de prueba`
- Estado: Agent Todo
- Label: agent-instructions

El agente lo toma automáticamente en su próximo ciclo.
```

---

*"Transporte con corazón" ❤️ — Metropolitana de Transportes La Carolina*
