# Dashboard de reclutamiento + Ads — La Carolina

Use this reference when Lily asks for a La Carolina dashboard/report that connects recruitment, social media, paid ads, Meta/WhatsApp, or Gestión Humana Excel data.

## User preference lessons

- Plan first when a dashboard/model is evolving; Lily wants the model and logic before uncontrolled implementation.
- Avoid overwhelming dashboards: prioritize a visual executive layer, then tabs/details.
- For recruitment dashboards, Lily prefers a **funnel visual** with La Carolina data rather than generic cards.
- Make the section “Qué nos muestra” legible and diagnostic, not decorative: use clear KPI cards with what it means and what to do.
- Include filters by **month/period** and **campaign** where data supports it. If exact campaign attribution is missing, show channel/campaign summary and label attribution as approximate.

## Recommended dashboard structure

1. **Embudo GH**
   - Postulados
   - Pasan filtro de perfil
   - Continúan proceso
   - Exámenes médicos
   - Aptos médicos
   - Contratados

2. **Indicadores Ads / Redes**
   Replace generic “technologies used” strips with useful operational KPIs, e.g.:
   - gasto Meta
   - impresiones
   - alcance
   - conexiones WhatsApp
   - primeras respuestas
   - tasa de primera respuesta
   - costo por postulado GH
   - costo por candidato válido
   - costo por contratado

3. **Ads ↔ Reclutamiento tab**
   Show the chain:
   `Ads → WhatsApp → Primera respuesta → Hoja de vida / GH → Candidato válido → Contratado`

4. **Filtros**
   - Período: historical, year, month, report period.
   - Canal: WhatsApp, Referido, Computrabajo, ManyChat, Varylo, Otros.
   - Campaña: only exact when the Excel has `CAMPAÑA / ORIGEN EXACTO` or `NOMBRE CAMPAÑA META`; otherwise show aggregated campaign periods such as Jaime vs JADS and mark as approximate.

## Data availability rules

- Filtering by month is available if the GH Excel has `FECHA DE CREACION`.
- Filtering by channel is available if the GH Excel has `MEDIO DE POSTULACION`.
- Exact campaign filtering is **not** available until GH/Marketing fills campaign fields such as:
  - `CAMPAÑA / ORIGEN EXACTO`
  - `CODIGO ENTRADA WHATSAPP`
  - `CANAL AGRUPADO`
  - `NOMBRE CAMPAÑA META`
  - `FECHA PRIMER CONTACTO`
  - `RESPUESTA INICIAL`
  - `CANDIDATO VÁLIDO ADS`
  - `OBSERVACIÓN MARKETING`

## Safe execution pattern for SharePoint Excel

1. Resolve/download the Excel through Microsoft Graph using the corporate account.
2. Before editing structure, create a backup. If copying in the same SharePoint folder fails, upload the backup to Lily’s corporate OneDrive project folder.
3. Add tracking columns to a copy first.
4. Try updating the live workbook only after backup exists.
5. If Graph returns `423 resourceLocked`, do not force the overwrite. Upload a `TRACKING_READY` copy and tell Lily/GH to replace or copy columns when the workbook is unlocked.

## OneDrive project folder convention

For La Carolina recruitment dashboards/campaign conductores, create/use:

`Transporte/MTC/05_Diseños/Dashboard Reclutamiento 2026 - Campaña Conductores`

Subfolders:
- `01_Fuentes`
- `02_Dashboard`
- `03_Excel_Tracking`
- `04_Backups`
- `05_Notas_y_Modelo`

Upload source PDFs, dashboard HTML, tracking-ready Excel, backups, and README/model notes there.

## Latest PDF analysis signals to preserve

When using `Informe-Reclutamiento-Meta-LaCarolina.pdf`, extract and prioritize:
- ~9,157 WhatsApp conversations in 6 months.
- 973 CVs/hojas de vida registered.
- 88 hired total; 54 via Meta.
- Real cost per Meta driver around $302K.
- Up to ~$2.6M per driver when agency cost is included.
- About 6 hires per 1,000 WhatsApp conversations.
- Referrals convert much better than paid channels.
- Recommended actions: run 3+ WhatsApp campaigns, exclude discarded/hired audiences, reconnect ManyChat filters, add campaign-origin column + UTMs, and review by day/ad/ad set with the agency.
