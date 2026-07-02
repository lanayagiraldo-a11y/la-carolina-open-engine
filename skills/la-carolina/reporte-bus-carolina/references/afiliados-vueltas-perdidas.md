# Reporte de afiliados — vueltas/novedades perdidas

Use when Lily asks for a **reporte para afiliados**, **vueltas perdidas**, **novedades del chat de afiliados**, or a ranking de buses/propietarios, not just a single bus/conductor.

## Sources

Preferred local working folder from the June 2026 workflow:

- `/root/.hermes/document_cache/mtc_afiliados_2026/WhatsApp Chat - Afiliados La Carolina_unzipped/_chat.txt`
- `/root/.hermes/document_cache/mtc_afiliados_2026/FLOTA DE VEHICULOS.xlsx`
- Obsidian owner fallback: `empresas/la-carolina/operacion/listado-afiliados-buses-propietarios.md`

If files moved, search for `WhatsApp Chat - Afiliados La Carolina.zip`, `_chat.txt`, and `FLOTA DE VEHICULOS.xlsx` before asking Lily.

## Parsing rules

- WhatsApp report lines commonly look like `780:(01)`, `803(02) cond en otro vehículo`, or natural text like `841 sin conductor`.
- Cause normalization:
  - `01`, `sin conductor`, `falta conductor` → `sin_conductor`.
  - `02`, `varado`, `taller`, `falla`, `mantenimiento`, `sensor`, `reparación` → `varado_taller_falla`.
  - Preserve explicit causes: `accidente`, `incapacidad`, `no contesta`, `disponible/salió de taller`, `otra`.
- Deduplicate by **fecha + bus + causa** so repeated mentions in the same daily message do not inflate counts.
- Cross with flota by bus/internal to add propietario, placa, ruta, modelo. If owner is missing in `FLOTA DE VEHICULOS.xlsx`, use the Obsidian `listado-afiliados-buses-propietarios.md` fallback.

## Required outputs

Generate at least:

1. Executive HTML with La Carolina branding: black `#222222`, red `#C22219`, gold `#DCBE61`.
2. Excel audit workbook with sheets:
   - `Resumen_mes`
   - `Causas_por_mes`
   - `Mes_bus_propietario`
   - `Ranking_buses`
   - `Ranking_propietarios`
   - `Causas_total`
   - `Detalle_mensajes`
3. PDF if possible; if Chromium Snap cannot read/write under `/root/.hermes`, serve the HTML through a local `python -m http.server` and use Playwright/Chromium against `http://127.0.0.1:<port>/...`.

## Report content

Always include:

- Period detected from the chat.
- Total deduplicated novedades.
- Number of internals/buses affected.
- Number of propietarios affected.
- Cause breakdown.
- Top propietarios by total novedades and buses affected.
- Top buses by total novedades.
- Monthly analysis before the accumulated view.
- Actionable recommendations for affiliate follow-up.

## Validation

Before delivery, verify:

- HTML exists and has non-trivial size.
- Excel exists and contains the required sheets.
- PDF exists and `pdfinfo` returns metadata when generated.
- Mention the deduplication rule in the response so Lily knows the count is not inflated.
