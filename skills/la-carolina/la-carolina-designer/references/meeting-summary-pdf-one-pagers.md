# La Carolina — Meeting-summary executive one-pagers

Use this reference when Lily asks for a brief PDF from a meeting, especially phrasing like “dame los 5 puntos”, “los pendientes”, “con branding Carolina”, “para la próxima reunión”, or “resumen ejecutivo”.

## Output shape

Default to a **single-page executive PDF** unless Lily asks for a full report. Include the editable HTML source as a secondary deliverable when practical.

Recommended structure:

1. **Branded cover/header**
   - Logo La Carolina when available.
   - “Metropolitana de Transportes La Carolina”.
   - Slogan: “Transporte con corazón”.
   - Meeting title, date, source, next-meeting date if known.
2. **Main section: 5 points principales**
   - Exactly five numbered cards.
   - Each card: short title + 1–2 sentence operational reading.
   - Keep language direct and management-ready; avoid transcript-like detail.
3. **Priority section: 3 pendientes prioritarios**
   - Exactly three cards.
   - Each card: pending item, responsible if known, date/plazo.
   - If date is unknown, say “fecha por definir” rather than inventing.
4. **Footer**
   - “La Carolina · [reunión/tema]” and “Transporte con corazón”.

## Branding rules

Use the official palette and style from this skill:

- Rojo Carolina `#C22219`
- Dorado Bus `#DCBE61`
- Negro/Grafito `#222222`
- Blanco `#FFFFFF`
- Optional beige support for report backgrounds: `#fbf5e6`

Typography:

- Bebas Neue for large section titles and numbers.
- Montserrat for body, dates and labels.

Visual language:

- High-contrast black/red/gold.
- Chevrons `>>>` and route-line accents are appropriate.
- Cards should be legible on phone screenshots and printable as PDF.

## Content discipline

- Do not include all meeting details when Lily asks for a brief; prioritize the top 5 and top 3.
- Preserve dates exactly as found in the summary/transcript/tool output.
- When the meeting has many unresolved items, choose the three that are either date-bound, assigned, or prerequisites for the next meeting.
- If source is Plaud, mention `Fuente: Plaud` in the document, but keep the user-facing summary clean.

## Layout and spacing standard

When Lily asks for a PDF “con branding Carolina”, do not default to dense ReportLab tables unless the document is purely technical. For executive meeting PDFs, prefer an editable HTML/CSS source rendered with browser/Playwright so spacing, cards, chips, logo placement and page rhythm can be controlled visually.

If Lily says “arregla los espacios”, “que sea gráficamente mejor”, or similar, treat it as a design correction, not just a minor edit:
- Increase white space and visual hierarchy even if the document grows from 1–3 pages to 4 clean pages.
- Use generous cards, section pages, balanced margins and fewer items per page.
- Avoid cramped multi-column tables when cards or grouped sections read better.
- Remove accidental overflow/blank spillover pages; content that continues onto an extra mostly-empty page is a layout bug.
- Keep the Carolina visual language visible on each page: top black band, red rule, logo, red/gold chips, footer line.

## Delivery checklist

- Generate an HTML file in `/root/.hermes/document_cache/`.
- Render to PDF using the available browser/Playwright path when possible.
- Verify the PDF exists and has non-trivial size before final reply.
- Render/inspect preview images for **all pages or at least first, middle, and last page** before delivery; do not only check page 1.
- Confirm there are no cut-offs, overlaps, blank spillover pages, or awkward empty pages.
- Deliver both:
  - `MEDIA:/root/.hermes/document_cache/<name>.pdf`
  - `MEDIA:/root/.hermes/document_cache/<name>.html`

## Example title

`La Carolina · Reunión afiliados — 5 puntos principales + 3 pendientes`
