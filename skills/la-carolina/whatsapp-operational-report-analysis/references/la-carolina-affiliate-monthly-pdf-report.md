# La Carolina affiliate monthly PDF report pattern

Use this reference when Liliana asks for an `informe afiliados`, `informe buses afiliados`, or asks to include `análisis por mes` in a La Carolina operational affiliate report.

## Durable lesson from 2026-06-03 session

The user wanted the affiliate/bus analysis to include a **month-by-month view**, not only accumulated rankings. The final deliverable that worked was an 11-page branded PDF generated from the existing affiliate Excel, validated visually and by text extraction.

## Required analytical shape

Include both accumulated and monthly cuts:

- Consolidated executive summary for the whole available date range.
- Monthly pages for each month actually present in the data, e.g. `2026-01` through `2026-06`.
- For each month:
  - top buses/internos by total novelty days;
  - top propietarios/afiliados by total bus-days;
  - concept breakdown: concepto 1/falta de conductor, concepto 2/varado-taller-falla, otros conceptos;
  - short observation about the dominant issue for that month.
- Accumulated rankings at the end:
  - buses/internos;
  - propietarios/afiliados;
  - otros conceptos by owner/cause when available.
- Do not infer months absent from the export/data.

## Branded PDF style that matched La Carolina

Replicate the existing La Carolina report identity when making a PDF:

- Logo centered or small header logo, extracted from an existing report when available.
- Thin red header line.
- Red section titles and red table headers.
- Brand-like red: `#C00000` is a good default.
- Clean corporate typography, generous spacing, readable tables.
- Keep a professional white-background report style.

## Layout pitfall and fix

A side-by-side monthly table layout was too cramped. The successful fix was to use **stacked monthly tables** instead of side-by-side tables. Prefer readability over trying to fit too many rankings on one page.

## Verification checklist

Before reporting completion:

1. Generate the PDF under `/root/.hermes/document_cache/...` for Telegram delivery.
2. Render at least representative pages to PNG and visually check:
   - cover/summary page;
   - one monthly page;
   - final accumulated ranking page.
3. Extract PDF text to confirm month labels, rankings, and key sections are present.
4. Attach the final PDF directly in Telegram when possible.

## Related Obsidian notes

- `/root/obsidian-vault/empresas/la-carolina/operacion/listado-afiliados-buses-propietarios.md`
- `/root/obsidian-vault/empresas/la-carolina/operacion/informe-afiliados.md`
