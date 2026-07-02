# La Carolina affiliate owner map

Use this reference when analyzing La Carolina WhatsApp bus novelty reports and the user asks to include afiliados/propietarios or owner-level rankings.

## Persistent source of truth

- Obsidian note: `/root/obsidian-vault/empresas/la-carolina/operacion/listado-afiliados-buses-propietarios.md`
- Purpose: maps internal bus number (`Interno`) to private owner / affiliated proprietor (`Propietario`).
- Created from the operational workbook `Analisis por busetas 2025-2024.xlsx`; treat as operational reference and mention it may need refreshing if the user requests current fleet status.

## How to use it

1. Read the Obsidian note before producing owner-level bus analysis.
2. Join parsed WhatsApp report rows on the normalized three-digit internal bus number.
3. Add at least one owner/affiliate column to the detailed Excel sheet.
4. When useful, add owner-level aggregation sheets/rankings:
   - total bus-days by owner,
   - concept 1 / falta de conductor by owner,
   - concept 2 / varado-taller-falla by owner,
   - count of distinct buses per owner.
5. If a bus from the WhatsApp report is absent from the map, keep it in the analysis with owner value `NO ENCONTRADO EN LISTADO AFILIADOS` and list it in limitations.

## La Carolina default code interpretation

- `1` = sin conductor / falta de conductor.
- `2` = varado, taller, falla, mantenimiento-related unavailability.

## Delivery expectation

For Liliana, owner-level analysis should be delivered as real files, preferably:

- PDF executive summary under `/root/.hermes/document_cache/...`
- Excel workbook under `/root/.hermes/document_cache/...`

Send both as Telegram attachments when available, not only as local paths.
