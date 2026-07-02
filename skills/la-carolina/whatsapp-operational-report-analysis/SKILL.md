---
name: whatsapp-operational-report-analysis
description: Analyze exported WhatsApp operational-report chats into auditable Excel summaries, especially La Carolina bus novelty reports.
platforms: [linux, macos]
---

# WhatsApp Operational Report Analysis

Use this skill when Liliana sends or links an exported WhatsApp chat and asks to analyze operational reports, recurring incidents, bus availability, varados/taller, falta de conductor, or to produce an Excel/CSV dashboard from chat messages.

## Core workflow

1. **Get the export**
   - Prefer a WhatsApp export **sin archivos** when text analysis is enough.
   - Accepted inputs: Telegram upload, Google Drive file link, OneDrive file/folder, `.zip`, or `_chat.txt`.
   - If Telegram upload fails, suggest Google Drive/OneDrive and then download via the appropriate Workspace skill.

2. **Extract safely**
   - If ZIP: inspect entries and extract `_chat.txt`.
   - Normalize WhatsApp invisible/control characters before parsing: `\u200e`, `\u200f`, bidi marks, `\u202f`, `\xa0`.
   - WhatsApp iPhone Spanish exports commonly use headers like:
     ```text
     [24/01/26, 11:00:58 a. m.] Javier Dir. Operativo: mensaje...
     ```

3. **Identify the official reporter**
   - For La Carolina bus-affiliate reports, prioritize messages from `Javier Dir. Operativo` unless the user specifies another sender.
   - Other senders may contain permissions, reactions, images, or maintenance notices; keep them separate unless explicitly requested.

4. **Classify report lines**
   - Parse leading three-digit bus numbers, allowing variants such as:
     - `780:(01)`
     - `803(02) cond en otro vehículo`
     - `884:Sin conductor`
     - `552 Accidente`
   - Default La Carolina mapping:
     - `1` / `01` / `sin conductor` = **concepto 1: falta de conductor**.
     - `2` / `02` = **concepto 2: varado / taller / falla**.
     - No numeric 1/2 or notes such as `accidente`, `sensor`, `mantenimiento`, `disponible`, `cond otro veh` = **otros conceptos**, unless the user gives a more specific mapping.
   - Count one bus appearing in one official daily report as **one day** per concept. Deduplicate repeated `bus + fecha + concepto` so duplicate lines do not inflate the result.

5. **Enrich La Carolina bus reports with affiliate owners when relevant**
   - If Liliana asks for afiliados, propietarios, dueños, or analysis “por propietario”, use the owner map in `references/la-carolina-affiliate-owner-map.md`.
   - Read the persistent Obsidian source before joining: `/root/obsidian-vault/empresas/la-carolina/operacion/listado-afiliados-buses-propietarios.md`.
   - Join by normalized three-digit internal bus number.
   - Keep unmatched buses in the analysis with an explicit placeholder and list them as a limitation; do not drop them.

6. **Produce an auditable Excel**
   Required sheets:
   - `README`: source file, date range, analyzed sender, classification rules, limitations, and owner-map source when used.
   - `Resumen_mes_bus`: mes, bus, owner/affiliate when available, días concepto 1, días concepto 2, días otros, total, notes.
   - `Ranking_buses`: total days by bus, sorted by total and then concept 2.
   - `Ranking_propietarios` when an owner map is used: total bus-days, concept 1 days, concept 2 days, other days, distinct buses.
   - `Rachas_consecutivas`: consecutive-day streaks by bus/concept.
   - `Detalle_dias`: fecha, bus, owner/affiliate when available, concept, original line, original message snippet.
   - `Mensajes_<sender>`: full official report messages for audit.
   - `Lineas_no_parseadas`: any lines with possible bus numbers that did not parse.

## When Liliana asks for an informe afiliados / PDF, include a month-by-month analysis
   - Do not deliver only accumulated totals. Add a monthly cut for every month actually present in the data.
   - Each month should show top buses/internos, top propietarios/afiliados, and the breakdown of concepto 1, concepto 2, and otros conceptos.
   - Preserve the accumulated rankings as final context, but make the monthly pages/readout explicit.
   - For La Carolina branded PDFs, follow `references/la-carolina-affiliate-monthly-pdf-report.md`.

## La Carolina PDF branding requirement

When Liliana asks for a PDF report for La Carolina, especially operational/committee reports, apply La Carolina branding by default — do not wait for her to correct it.

- Use the verified La Carolina logo when available, e.g. `/root/obsidian-vault/control-interno-sistema/public/assets/logo_la_carolina.png` or a current logo asset in `/root/.hermes/document_cache/la-carolina-meta-ads-site/assets/`.
- Use the corporate palette visible in the logo: red, gold, black/charcoal, and clean white/off-white backgrounds.
- Include an institutional cover page with logo, title, date range, source, and executive KPI cards.
- Add branded headers/footers on interior pages: small logo or report title, red/gold rule, page number, and “La Carolina · Transporte con corazón” when appropriate.
- Style charts with the same red/gold/black palette instead of generic report colors.
- Keep the report executive and board-ready: insight first, then monthly evolution, concentration/ranking, categories, rachas/cases críticos, and recommendations for comité.
- Verify the PDF exists, has non-trivial file size, and can be parsed/opened before delivering it as a Telegram attachment.

## Topic-boundary guard: affiliates vs Transmetro

- If the active user topic is explicitly **Transmetro**, the Transmetro simulator, evasión TM, FET/FQ, TPC cascade, or Metrocaribe/Transmetro finance, do **not** answer from the WhatsApp affiliate/bus-novelty workbook just because the previous turn mentioned “informe afiliados”.
- Only use this affiliate-report workflow when Liliana is asking about afiliados/propietarios/buses/novedades/varados/conductores or the WhatsApp bus reports.
- When she corrects the topic (e.g. “estamos solo analizando Transmetro”), immediately reset context to the named topic and avoid carrying over insights from the previous operational report.

8. **Report limitations plainly**
   - Always state the date range actually present in the export. If the user asks for “último año” but the chat only includes January–June, say so and analyze only the available range.
   - Do not infer missing months from absence of messages.

7. **Deliverable handling**
   - Create the Excel under `/root/.hermes/document_cache/...`.
   - For Telegram, send the `.xlsx` as a native attachment via `send_message` when available; avoid only printing local paths.

## Output style for Liliana

Use a concise Spanish summary:
- “Ya procesé el chat y te adjunto el Excel.”
- Mention range available.
- Give top 5 buses by total days with breakdown by concept.
- Mention deduplication rule.

Do not over-explain parsing internals unless she asks; keep audit detail in Excel sheets.

### Completion boundary / do not repeat finished deliverables

If the report has already been generated, verified, and sent in the current conversation, do **not** regenerate or resend it just because the user acknowledges or confirms (“sí”, “gracias”, etc.). Treat the deliverable as closed unless Liliana explicitly asks for a revision, a new cut, or a re-send. If she says the task is already finished or complains that it is being repeated, acknowledge briefly and move to the next active task instead of restating the same report contents.

### Completion-state guard

If the affiliate report has already been generated, verified, and sent in the current session, do **not** regenerate or resend it just because the topic appears again in compressed context or active history. If Liliana says “ya lo terminaste”, “sigues repitiendo”, or similar, immediately acknowledge that the affiliate report is complete, stop repeating the completed task, and move back to the next active pending task. Only reopen this workflow if she explicitly asks for a revision, new data, or a different deliverable.

## Executive insight readout after the workbook/PDF

When Liliana asks for “insights”, “inside”, “qué detectaste”, or wants the managerial reading of an affiliate/bus novelty analysis, do not only restate rankings. Use the already-generated Excel sheets to produce a short executive interpretation:

- Re-open `Resumen_mensual`, `Ranking_buses`, `Ranking_propietarios`, `Mes_propietario`, `Mes_bus_propietario`, `Rachas_consecutivas`, and any `Otros_*` sheet if present.
- State the actual date range and flag partial months (for example, June only through day 2 should not be compared as a full month).
- Identify whether the dominant issue changes over time: e.g. early months dominated by falta de conductor vs later months dominated by varado/taller.
- Report the most critical month by total bus-days and its concept breakdown.
- Report concentration: top 1/3/5/10 owners and buses as a percentage of total bus-days, because this reveals whether the intervention can be focused.
- Separate owners by primary problem type: conductor, taller, or otros. Recommend different action tracks instead of one generic measure for all affiliates.
- Highlight case-level outliers: the single worst bus, the worst owner, and any long consecutive streaks (3+ days is an operational alert; 5–6+ days deserves escalation).
- Use monthly leaders (`Mes_propietario`, `Mes_bus_propietario`) to explain that the risk can migrate month to month.
- If “otros” contains recurring subcauses such as sensor, accidente, no contesta, incapacidad, etc., mention whether any should become its own category in future reports.
- Finish with an actionable committee-style conclusion: which buses/owners to address first and whether the proposed action is conductor management, maintenance/taller, or clarification of special causes.

### Cross-analysis with absenteeism, availability, and economic proposals

When Liliana provides both a driver absenteeism analysis and an affiliate bus-availability report, connect them explicitly rather than treating them as separate reports:

- Translate affiliate novelty totals into operational capacity language: `bus-días no trabajados / días calendario del periodo = promedio de buses afiliados fuera de operación por día` and, when the affiliate fleet size is known, `bus-días no trabajados / (buses afiliados × días calendario) = approximate fleet-day loss rate`.
- Separate the operational causes clearly: **falta de conductor** links to absenteeism/rotación/reemplazos/retención; **varado/taller** links to mantenimiento, taller, repuestos, and owner compliance; **otros** should be reviewed for recurring subcauses before mixing it with either bucket.
- If the user mentions Portal de Soledad, propuesta económica, deuda, recuperación de flota, or “buses afiliados que no salen a trabajar”, frame the finding as recoverable operating capacity: affiliate buses registered on paper are not enough; measure effective salida, cause of non-operation, and corrective plan by owner.
- For proposals where a constructor/developer would pay or offset La Carolina debt, connect operations to finance: define what service/operational commitment La Carolina gives in exchange, which debt balance is covered, payment schedule/escrow or direct creditor payment, and measurable KPIs (buses actually dispatched, trips fulfilled, punctuality, availability, incident response, and reporting cadence). Do not present it only as “new income”; make the debt-payment mechanism and performance obligations explicit.
- For committee wording, use concise executive phrasing such as: “La Carolina has an average of X affiliate buses out of operation per day; more than half is mechanical/taller and around Y% is falta de conductor. This makes affiliate management an immediate source of operational and financial recovery before buying new buses.”
- Avoid claiming true staff turnover unless the source includes ingreso/retiro/cambio definitivo fields. If only absenteeism and bus novelty reports are available, say “rotación operativa/inestabilidad por bus-conductor” or “señal indirecta”, not formal turnover.

## Pitfalls

- For monthly affiliate PDF reports, side-by-side monthly tables can become unreadable. Use stacked sections/tables per month and verify representative pages visually before delivery.
- WhatsApp exports may contain multi-line messages; do not parse line-by-line without first grouping by message header.
- Sender names can include invisible Unicode; normalize before comparing.
- `cond en otro vehículo` may appear beside `(02)`. Preserve it in notes, but keep classification driven by the numeric code unless the user gives a different operational meaning.
- Images omitted (`imagen omitida`) are not evidence for text counts; only count text report lines.
- Telegram may reject large WhatsApp ZIPs; use Google Drive/OneDrive links instead of retrying the same upload path.
