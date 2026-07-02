---
name: la-carolina-data-intelligence
description: Query Supabase, aggregate data in Python, and generate HTML+Chart.js business intelligence dashboards for La Carolina. Covers multi-table analysis (viajes, conductores, ausentismo, propietarios, cierres), rutas críticas, unidad de negocio analysis, and operational KPIs.
tags: [la-carolina, supabase, dashboard, analytics, python, chartjs]
---

# La Carolina — Data Intelligence

Generate HTML dashboards analyzing La Carolina operational, financial, and HR data from Supabase. Uses REST API queries, Python aggregation, and Chart.js visualization with La Carolina branding (dorado #DCBE11, negro #222222, rojo #C22219).

## When to use

Activate when Liliana asks:
- "Analiza el afiliado X" / "dame un análisis de los buses de X"
- "Cruce con reclutamiento" / "medio de postulación de los conductores"
- "De dónde vino este conductor" / "cómo entró X a la flota"
- "Top rutas críticas" / "dónde se pierden más viajes"
- "Dame un análisis de X y Y" (comparative business unit analysis)
- "Quiero saber el desempeño de..." (multi-metric analysis across tables)
- "Informe flota completa" / "todos los buses de la familia X"
- Any request involving cross-table Supabase data + visual dashboard + recruitment cross-reference

## Workflow

### Phase 1: Connect to Supabase

Credentials are in `~/.hermes/pastes/paste_1_203014.txt`:
- URL: `https://exloqxhnzplovndcjale.supabase.co`
- Service role key is stored in the paste file

Use pagination for tables over 1,000 rows:

```python
def get_all(table, **params):
    data, offset = [], 0
    while True:
        r = requests.get(f"{URL}/rest/v1/{table}", headers=h,
            params={**params, "limit": 1000, "offset": offset})
        batch = r.json()
        if not batch: break
        data.extend(batch); offset += len(batch)
    return data
```

### Phase 2: Identify the data scope

| Question type | Tables needed | Link columns |
|---|---|---|
| Business unit (propietario) analysis | viajes_recaudados, viajes_perdidos, conductores | propietario_nombre, placa, cedula |
| Route analysis (rutas críticas) | viajes_perdidos | ruta, placa |
| Driver performance | conductores, viajes_recaudados, ausentismo, incentivos | cedula |
| Financial summary | cierres_diarios, viajes_recaudados | fecha, codigo_vehiculo |

**Critical pitfall:** Vehicle codes in viajes_recaudados (3-digit `codigo_vehiculo`: 500–999) do NOT match propietario codes (4-digit: 4190, 4288). Always link via `propietario_nombre` (use `ilike.*search*`) or `cedula_propietario`.

**Internal number discovery:** The `codigo_vehiculo` column in `viajes_recaudados` is the 3-digit bus number (e.g., WGA422 → 619). To map plates to internal numbers: query one plate at a time with `{"select": "codigo_vehiculo", "placa": f"eq.{placa}", "limit": 1}`.

### Phase 3: Aggregate in Python

Use `collections.Counter` for counting, `defaultdict` for sums, collect into dict for HTML template:

```python
from collections import Counter, defaultdict
placas = set()
conductores = Counter()
total_bruto = 0
for row in data:
    placas.add(row.get('placa',''))
    conductores[row.get('conductor_nombre','')] += 1
    total_bruto += row.get('bruto', 0) or 0
```

### Phase 4: Generate HTML dashboard

Build a self-contained HTML file with:
- Chart.js from CDN (`https://cdn.jsdelivr.net/npm/chart.js@4`) — if charts don't render on mobile/Telegram, switch the last 2 charts to inline SVG (see `references/mobile-svg-charts.md`)
- KPI cards at top (use `.kpi-grid` pattern)
- Tables for breakdowns with bar visualization
- La Carolina branding: dark background (`#0d0d12`), gold accent (`#f7c915`/`#DCBE11`), 3-digit color palette
- Embed data as JSON in a `<script>` block in the page

**File path convention:** `~/proyectos/la-carolina-data/dashboard/<topic>.html`

**Delivery preference (observed):** When the user asks for an HTML file, do NOT deploy to Netlify or offer to deploy unless explicitly requested. The user's pattern is: "dame esto en html sin deploy" / "incluye esto en el html". Deliver the file path for local download via `MEDIA:<path>` and let the user decide when to publish.

### Phase 5: Render + deliver

For Telegram delivery:
1. Serve with `python3 -m http.server <PORT>` in the dashboard directory
2. Screenshot with Playwright (headless Chromium)
3. Deliver with `MEDIA:<path>` for the screenshot
4. Tell the user the HTML file path

### Phase 5 (alternative): Generate Telegram-formatted text report

When Liliana asks for a fleet overview on Telegram (not a dashboard), output a **structured text report** with these sections:

1. **Global KPIs** (table format, 1 line per metric)
2. **Per-propietario breakdown** — each owner gets a header with bus table, driver count, lost trips %, top cause
3. **Rotation table** — unique drivers per month per propietario (shows churn pattern)
4. **New drivers** — total nuevos, by month, most recent entries with trip counts
5. **Incentivos coverage** — total paid, beneficiaries, top recipients, and critically: **top drivers WITHOUT incentives** (gap analysis)
6. **Ausentismo** — total days lost, top cases
7. **Top 15 drivers** — ranked by viajes with bruto and months active
8. **Insights** — 5 bullet points with findings

Format rules:
- Use Telegram Markdown: `**bold**`, tables with `| col | col |`, emojis for semantic categories (📊 totales, 🟢🟡🔴 status, 🏢 per-owner, 🎁 incentivos, 🏥 ausentismo, 🏆 top drivers)
- Tables right-align numbers with `---:` in header separator
- DO NOT pad numbers with spaces inside cells — Telegram rendering handles alignment
- Each section separated by `---` horizontal line
- Never include SQL, Python code, or technical details in the final report — that goes in the thinking

## Multi-propietario family analysis

When Liliana asks about a **family of propietarios** (e.g., "Rueda", "Ortega", "González"):

### Step 1: Find all family members

Query `viajes_recaudados` with `propietario_nombre=ilike.*FAMILYNAME*` to discover all propietarios sharing the surname:

```python
r = requests.get(f"{URL}/rest/v1/viajes_recaudados", headers=h,
    params={"select": "propietario_nombre,placa", 
            "propietario_nombre": "ilike.*RUEDA*", "limit": 2000})
```

This reveals all distinct propietario names and their plates in one query.

### Step 2: Build plate-to-propietario map

```python
propietarios_map = {"WGA422": "Hermes A. Rueda", ...}
```

### Step 3: Query per-plate with pagination

**Critical technique:** Query one plate at a time, not all plates in an `in.()` filter — Supabase may truncate large in-filter results:

```python
for placa in all_rueda_plates:
    data = get_all(url, "viajes_recaudados",
        {"placa": f"eq.{placa}", "fecha_viaje": "gte.2026-01-01",
         "select": "placa,conductor_nombre,bruto,timbradas,fecha_viaje,pasaje"},
        limit=2000)
```

### Step 4: Cross-reference incentivos and ausentismo

Build the set of all conductor names found in the fleet's viajes, then filter incentivos and ausentismo tables:

```python
all_rueda_cond = set(cn for b in buses_data.values() for cn in b["conductores_detalle"])
incentivos_rueda = [i for i in incentivos_all 
                    if i.get('nombre','').strip().upper() in all_rueda_cond]
ausentismo_rueda = [a for a in ausentismo_all 
                    if a.get('nombre','').strip().upper() in all_rueda_cond]
```

### Step 5: Calculate rotation metrics

```python
# Unique drivers per month per propietario
rotacion_mensual = defaultdict(lambda: defaultdict(set))
for v in all_viajes:
    m = v.get('fecha_viaje','')[:7]
    cn = v.get('conductor_nombre','').strip()
    prop = propietarios_map.get(v.get('placa',''), '?')
    if m and cn:
        rotacion_mensual["TOTAL"][m].add(cn)
        rotacion_mensual[prop][m].add(cn)
```

### Step 6: Identify new drivers (first-trip-in-2026)

```python
first_trip = {}
for v in all_viajes:
    cn = v.get('conductor_nombre','').strip()
    fv = v.get('fecha_viaje','')
    if cn and fv and (cn not in first_trip or fv < first_trip[cn]):
        first_trip[cn] = fv

new_in_2026 = {cn: fv for cn, fv in first_trip.items() if fv >= "2026-01-01"}
```

### Step 7: Benefit coverage gap analysis

The key insight: compare top drivers (by viajes count) against incentivo recipients to find **high-value drivers without benefits**:

```python
top_sin_incentivo = [(cn, cnt) for cn, cnt in all_cond_totals.most_common(30)
    if not any(i.get('nombre','').strip().upper() == cn.upper() for i in incentivos_rueda)]
```

### Step 8: Classify drivers by activity level

**Critical correction pattern:** When reporting unique driver counts, the raw number (e.g., 69) is misleading because most are relief drivers. Always classify:

| Category | Criteria | Typical % |
|---|---|---|
| 🟢 Fijos | ≥100 viajes OR ≥3 months active | ~12 drivers (do ~90% of trips) |
| 🟡 Semifijos | 20-99 viajes OR ≥2 months | ~17 drivers |
| 🔴 Relevos | <20 viajes AND 1 month | ~40 drivers (appeared 1-3 times) |

```python
fijos = [cn for cn, st in cond_stats.items() 
         if st["viajes"] >= 100 or (len(st["meses"]) >= 3 and st["viajes"]/max(len(st["meses"]),1) >= 15)]
relevos = [cn for cn, st in cond_stats.items() 
           if st["viajes"] < 20 and len(st["meses"]) < 2]
semifijos = [cn for cn in cond_stats if cn not in fijos and cn not in relevos]
```

Present this **before** the raw unique count so the user can immediately see the real operational picture.

### Step 9: Cross-propietario driver sharing

Detect drivers who work across multiple propietarios within a family (e.g., Manjarres drives for Hermes AND Norberto):

```python
cross_prop = {cn: st for cn, st in cond_stats.items() if len(st["propietarios"]) >= 2}
```

This reveals the **family pool** of shared relief drivers — important context for rotation analysis.

## Accident data handling

The `viajes_perdidos` table records accidents as `ACCIDENTE TRANSITO` in the `novedad` column, but one physical accident generates **multiple rows** (one per repair day). To get true accident counts:

### Pitfall: One accident = N repair days

```python
# DO NOT count viajes_perdidos rows directly — that inflates the count
# Instead, group consecutive dates per bus:

from datetime import datetime, timedelta

bus_repair_days = defaultdict(list)
for a in all_accident_records:
    bus_repair_days[a['placa']].append(a['fecha'])

# Group consecutive dates (gap ≤2 days) into one accident event
eventos = []
for placa, fechas in bus_repair_days.items():
    fechas = sorted(set(fechas))
    current = [fechas[0]]
    for f in fechas[1:]:
        gap = (datetime.strptime(f, '%Y-%m-%d') - datetime.strptime(current[-1], '%Y-%m-%d')).days
        if gap <= 2:
            current.append(f)
        else:
            eventos.append((placa, current[0], current[-1], len(current)))
            current = [f]
    if current:
        eventos.append((placa, current[0], current[-1], len(current)))
```

### Accident severity classification

| `detalle_novedad` contains | Classification | Insurance? |
|---|---|---|
| `REPARACION DAÑOS ACCIDENTE` | Con daños — requiere taller | Reportable (business end) |
| `CON RECAUDO` | Leve — siguió operando | Typically not reported |

### Key accident metrics to report

- Total **unique accident events** (grouped, not per-repair-day)
- Total **eventos reportables** (with repair = likely sent to insurance)
- Total **días perdidos en taller** por accidentes
- **Accident rate** per bus and per propietario
- **Monthly trend** of accidents

**Caveat:** The DB has no column tracking whether a claim was actually filed with the aseguradora. That data lives in SCRA or with John Guerrero's team.

## Stale data pitfall in Supabase

`viajes_recaudados` and `viajes_perdidos` in Supabase may be snapshots, not real-time mirrors of GEMA. The `viajes_recaudados` table can lag behind the live GEMA MySQL database. When numbers seem low, cross-check against GEMA's stored procedures:
- `pa_ext_get_ViajesRecaudadosByFecha(fechaInicio, fechaFin)` (from GEMA MySQL via `la-carolina-data` skill)
- `pa_ext_get_ViajesByFecha(fechaInicio, fechaFin)`

## Recruitment data cross-reference

The recruitment database (`procesos_contratacion_raw.csv`) lives in the Obsidian vault at:
`empresas/la-carolina/dashboard-redes-ads-reclutamiento/data/procesos_contratacion_raw.csv`

It contains 3,654 records of all driver applicants with their **MEDIO DE POSTULACION** (source channel), **ESTADO DEL PROCESO**, and hiring dates.

### When to cross-reference

Activate when Liliana asks:
- "De dónde vino este conductor" / "cómo entró X"
- "Cruce con reclutamiento" / "medio de postulación de los conductores"
- "Dime el reclutamiento o el ingreso por qué medio es por conductor"
- Any request to link operational data (viajes, incentivos) with hiring source

### Workflow

1. **Get all driver names** from the fleet's `viajes_recaudados` data (build the set from per-plate queries).
2. **Load the CSV** and do fuzzy name matching (filter words shorter than 3 chars to skip DE/LA/DEL etc.; require >=2 significant word overlap).
3. **Key columns**: `MEDIO DE POSTULACION` (WHATSAPP, COMPUTRABAJO, REFERIDO, VARYLO, MANYCHAT), `ESTADO DEL PROCESO` (CONTRATADO, CIERRE DE PROCESO, CITADO), `FECHA DE CREACION`.
4. **Critical insight**: Many top drivers have "CIERRE DE PROCESO" status -- never formally contracted through GH. Some don't appear in recruitment DB at all (covering from other afiliados).
5. **"Not found" handling**: Always list top drivers missing from recruitment DB separately. Example: Abraham Cohen (478 viajes, #3 driver) was not in the DB because he likely drives for a different afiliado and does relief work for Rueda. This is not a data error — it's a structural pattern (cross-afiliado driver sharing).
6. **HTML deliverable**: source distribution chart (doughnut), contracted vs. non-contracted chart, full cross-reference table with color-coded status, and a "not found" callout for missing drivers.

## Quality checklist

- [ ] Data paginated completely (don't settle for 1,000 record limit)
- [ ] Cross-table links verified (propietario_nombre not codigo)
- [ ] KPIs meaningful (not just raw counts — rates, % where applicable)
- [ ] Chart.js chart renders without errors (HTML mode)
- [ ] **Mobile check**: if user reports charts not visible on mobile, replace last 2 Chart.js charts with inline SVG (see `references/mobile-svg-charts.md`)
- [ ] HTML is self-contained (no external dependencies beyond Chart.js CDN) (HTML mode)
- [ ] User not asked to touch Supabase dashboard — everything from Hermes
- [ ] Telegram text reports: tables with `:---` alignment, emojis, no code in output
- [ ] Multi-propietario analysis: query ONE plate at a time, not in-filter batches
- [ ] Benefit gap analysis: always check top drivers without incentivos
- [ ] **Driver classification**: always classify raw unique driver count into Fijos/Semifijos/Relevos before presenting to user
- [ ] **Cross-propietario sharing**: check which drivers work for 2+ propietarios (family pool)
- [ ] Recruitment cross-reference: match fleet drivers against `procesos_contratacion_raw.csv` via fuzzy name matching (>=2 significant words)
- [ ] Recruitment source distribution chart included in HTML (doughnut: WhatsApp/Referido/Computrabajo/etc.)
- [ ] Missing-driver callout: list top drivers not found in recruitment DB
- [ ] **Accident dedup**: group consecutive repair dates per bus into single accident events; distinguish "CON RECAUDO" (leve) from "REPARACION" (reportable)

## Route mobility analysis (least-performing routes)

When Liliana asks "qué rutas tienen menos movilidad" or "dame las rutas que menos han movido":

### Step 1: Get ALL viajes with route info

```python
# Query all viajes_recaudados with route columns
all_viajes = get_all(url, "viajes_recaudados",
    {"fecha_viaje": "gte.YYYY-01-01",
     "select": "fecha_viaje,placa,bruto,timbradas,ruta_programada,ruta_reprogramada"})
```

**Pagination note:** Use offset-based pagination (offset=0, limit=1000, repeat). The `in.()` filter with many values can truncate results. For full-company queries don't filter by plate.

### Step 2: Aggregate by route + month

Use a compound key `(ruta, mes)`:

```python
ruta_mes = defaultdict(lambda: {"viajes":0, "timbradas":0, "bruto":0, "buses":set()})
for v in all_viajes:
    ruta = (v.get('ruta_programada','') or v.get('ruta_reprogramada','') or 'SIN RUTA').strip()
    mes = (v.get('fecha_viaje','') or '')[:7]
    if not mes: continue
    key = (ruta, mes)
    ruta_mes[key]["viajes"] += 1
    ruta_mes[key]["timbradas"] += int(float(v.get('timbradas',0) or 0))
    ruta_mes[key]["bruto"] += int(float(v.get('bruto',0) or 0))
    ruta_mes[key]["buses"].add(v.get('placa',''))
```

### Step 3: Aggregate by route total and sort ascending

```python
rutas_totales = defaultdict(lambda: {"viajes":0, "timbradas":0, "bruto":0, "buses":set(), "meses":set()})
for (ruta, mes), data in ruta_mes.items():
    rutas_totales[ruta]["viajes"] += data["viajes"]
    # ...
rutas_ordenadas = sorted(rutas_totales.items(), key=lambda x: x[1]["viajes"])
```

### Step 4: Detect duplicate routes (data quality)

**Common pitfall:** Same route name with different formatting (e.g., `A - 16 MIRAMAR` vs `A -- 16 MIRAMAR` — single vs double dash). When found, note as a data quality issue and suggest Néstor unify the naming.

### Step 5: Monthly breakdown table

For the bottom routes, show a month-by-month table to reveal seasonal patterns:

```
Ruta                          Ene    Feb    Mar    Abr    May    Jun   Total
A -- 16 MIRAMAR              2,232  1,934  2,128  2,021  2,225  1,664  12,204
```

**Key insight:** All routes drop in June (end of school year / start of vacations). The weakest route drops most.

## Projection methodology (weekly avg × seasonality)

When asked for projections through year-end (or any forward-looking period):

### Step 1: Establish current weekly baseline

Average the most recent 3 complete weeks of data per plate:

```python
recent_weeks = ["Jun S2", "Jun S3", "Jun S4"]
y_avg = sum(weekly_data[g].get(w,0) for w in recent_weeks) / 3
```

Report per-bus averages too: `total_avg / num_plates`.

### Step 2: Define seasonality factors

For Colombian urban transport (Barranquilla):

| Month | Factor | Rationale |
|:---|:---:|---|
| Julio | 0.95 | Recovery from June dip |
| Agosto 🏖️ | 0.85 | Vacation month |
| Septiembre | 0.95 | Recovery |
| Octubre | 1.00 | Normal |
| Noviembre | 1.05 | Pre-Christmas high |
| Diciembre 🎄 | 0.70 | Christmas — lowest of year |

### Step 3: Calculate projection

```python
weeks_per_month = 4.33  # 52/12
for m_name, factor in seasonality:
    monthly = weekly_avg * factor * weeks_per_month
```

For weekly: `weekly_avg * factor` (same factor applies to all 4 weeks in that month).

### Step 4: Deliverable

Present as:
1. Real data table (past weeks)
2. Factor explanation
3. Monthly projection table
4. Weekly projection table
5. Summary with total projected timbradas and estimated revenue

## New fleet tracking (0km vehicles)

When asked about Yutong, Fotón, or "buses nuevos":

### Step 1: Identify new plates by first-appearance date

Query viajes_recaudados ordered by fecha_viaje ascending and find plates that first appeared after a certain date:

```python
plate_first = {}
for prefix in ["LJO", "LJC", "LPB", "LPC"]:
    r = requests.get(..., params={"placa": f"like.{prefix}%", "order": "fecha_viaje.asc", "limit": 2000})
    for v in r.json():
        p, f = v.get('placa',''), v.get('fecha_viaje','')[:10]
        if p not in plate_first or f < plate_first[p]:
            plate_first[p] = f

new_plates = {p: f for p, f in plate_first.items() if f >= "2026-04-01"}
```

### Step 2: Determine group by owner

Query `propietario_nombre` for each new plate:

```python
r = requests.get(..., params={"placa": f"eq.{placa}", "select": "propietario_nombre", "limit": 1})
owner = r.json()[0].get('propietario_nombre','')
```

- **Flota propia** (owner contains "LA CAROLINA") = Yutong
- **Sistemas Prácticos / leasing** = Fotón

### Step 3: Weekly timbradas per new plate

Same as any per-plate analysis — query one plate at a time, aggregate by week.

### Step 5: Build projection

Same methodology as above — use recent weekly average × seasonality factors.

**Key finding:** New vehicles take ~4-6 weeks to reach full operational capacity (ramp-up period visible in the data).

**Critical correction — do NOT guess brand/plate mapping:** The user corrected incorrect plate-to-brand assignment during a session. Always consult `references/new-fleet-plate-mapping.md` for the official VS-208 registry mapping before reporting Yutong/Fotón/Chevrolet MS assignments.

### Step 5b: Percentage contribution analysis

When asked what % of total company timbradas each tipología contributes:

1. **Get total company timbradas** (all viajes, no plate filter):

```python
total_all = 0
for m in range(1, 7):  # months 1-6
    start = f"2026-{m:02d}-01"
    end = f"2026-{m:02d}-28" if m == 2 else f"2026-{m:02d}-30" if m in [4,6,9,11] else f"2026-{m:02d}-31"
    r = requests.get(url + "/rest/v1/viajes_recaudados", headers=h,
        params={"and": f"(fecha_viaje.gte.{start},fecha_viaje.lte.{end})",
                "select": "timbradas", "limit": 10000})
    month_t = int(sum(float(v.get('timbradas',0) or 0) for v in r.json()))
    total_all += month_t
```

**CRITICAL:** Use `and=(fecha_viaje.gte.X,fecha_viaje.lte.Y)` syntax — Supabase REST does NOT accept duplicate `fecha_viaje` URL params. The `like.` filter also doesn't work for date prefixes. The `and=` filter is the only reliable way to specify a date range.

2. **Calculate percentages:**

```python
for group in ["Yutong", "Fotón", "Chevrolet MS"]:
    pct = group_total / total_all * 100
```

3. **Deliverable:** KPI cards showing each group's % + a summary insight ("14 buses = 33.5% of ALL company timbradas"). This is a powerful finding the user values.

### Step 5c: Per-bus per-month comparison table

When asked for a comparative table by tipología:

1. Query each plate individually with month-range filters (using `and=` syntax)
2. Sum timbradas per month per plate
3. Calculate `promedio/bus`: group total / number of buses in that tipología
4. Calculate `vs Fotón`: `(group_prom / foton_prom - 1) * 100` (Fotón is the productivity reference)
5. Report Abril parcial (only last week), Mayo/June complete months
6. **HTML deliverable:** Bar chart (timb/bus/mes per group) + line chart (monthly trend per group)

### Step 5: Weekly deep-dive deliverable

When asked for a week-by-week report desde entrada + proyección a diciembre:

1. **Build complete weekly grid**: generate all ISO weeks from Apr to Dec as `(label, start_date, end_date)`
2. **Query each plate individually** with `fecha_viaje` within each week's range
3. **Group by fleet** (Yutong/Fotón/Chevrolet MS using the official mapping)
4. **Print real data table**: one row per week with columns for each fleet group
5. **Calculate recent average**: use last 3 complete weeks (Jun S2-S4) as baseline
6. **Compute per-bus metrics**: divide group total by number of plates in that group
7. **Apply seasonality factors** (same as projection methodology above)
8. **Deliverable**: PDF with (a) page 1: week-by-week since entry per bus, (b) page 2: projection Jul-Dec weekly + monthly totals

**PDF format:** Use landscape (ReportLab `landscape(letter)`), font DejaVuSans for Spanish chars, dark header rows with gold accent.

## Available data sources (complete inventory)

### Supabase #1 (operacional)
- URL: `https://exloqxhnzplovndcjale.supabase.co`
- Tables: viajes_recaudados, viajes_perdidos, conductores, propietarios, incentivos, ausentismo
- Data range: 2026-01-01 → present (no 2025 data)
- Credentials: in `~/.hermes/pastes/paste_1_203014.txt`

### Supabase #2 (conductores master list)
- URL: `https://lqeddrpbwunzcyjxuiei.supabase.co`
- Tables: conductores (168 records, all active, placa_asignada all None), incentivos (empty)
- Very limited — only useful for driver master list lookup
- Anon key and service role key provided by user in chat

### GEMA MySQL (historical — CREDENTIALS EXPIRED)
- Host: `carolinainstance.c8twjwe0zioh.us-east-2.rds.amazonaws.com:3306`
- Database: `gema_cr`
- Views: conductores (1190), empleados (320), personal (1648), propietarios (153) + 6 SPs
- **Credentials expired** — user needs to renew with John/Edith

### SharePoint reclutamiento
- URL: `https://datastewarlacarolina.sharepoint.com`
- Archivo: "Procesos de reclutamiento.xlsx"
- Hoja principal: `PROCESOS DE CONTRA` (3,654 registros)
- Key column: `MEDIO DE POSTULACION`
- CSV exported to: `obsidian-vault/empresas/la-carolina/dashboard-redes-ads-reclutamiento/data/procesos_contratacion_raw.csv`
- Account: `lanaya@lacarolina.com.co`

## FQ / Superpolo — Capacity-to-pay analysis

When Liliana asks about the Fondo de Calidad, Superpolo credit, or whether the system can pay its obligations:

### Trigger phrases
- "¿Alcanza para pagar Superpolo?"
- "¿Podemos pagar el crédito completo del FQ?"
- "FQ vs Superpolo"
- "Recaudo FQ"
- "Golpe al FQ" / "Presión financiera"

### Workflow

1. **Load the data sources**: `INFORME COMITE FQ MAYO 2026.pdf` (recaudo 2026, cartera, saldo) + `PRESENTACION COMITE FQ 03 FEBRERO 2026.pdf` (recaudo 2025) + plan de pagos Superpolo

2. **CRITICAL: Give ONE clear answer first**, then add sensitivity context. The user explicitly corrected: "no quiero tres escenarios quiero saber realmente." Wrong: "aquí hay 3 escenarios..." Correct: "No, barely. Shortfall of ~$39M. If the decline worsens 5%, it grows to $1,457M." Also: "frases breves pero que den el mensaje" — keep every sentence carrying weight.

3. **Check the -23.6% meaning**: It's passengers, not $ — the rate doubled from $100→$200, so recaudo actually increased +40.7%. Explain this clearly.

4. **Check distinctions**: Never mix TPC / Transmetro / FQ / FET. Each is separate.

5. **Check data sources**: -23.6% is internal AMB/FQ from liquidation reports, not DANE. DANE is external contrast only.

6. **Caveat the projection**: recaudo is NOT linear. Month-to-month variability exists (see 2025 data: $644M in Feb vs $2,463M in Jul).

Full methodology: `skill_view(name="la-carolina-data-intelligence", file_path="references/fq-superpolo-analysis.md")`

## Presentation improvement methodology

When improving the `presentacion_estado_actual_sistema_2026.html` presentation:

1. **Add Chart.js CDN** in `<head>` for proper data visualization
2. **Slide 5 (Golpe al FQ)**: Replace text timelines with a bar chart of 2025 monthly recaudo (highlights variability)
3. **Slide 8 (La reserva entra en estrés)**: Replace verbose text blocks with a multi-line chart showing 3 reserve depletion scenarios. Keep summary metrics in compact cards below the chart.
4. **Always create/update README.md** with source references, structure, and update instructions
5. **Duplicate to `public/index.html`** for Netlify deployment
6. **Text corrections** from the CONTINUIDAD document (see references/fq-superpolo-analysis.md)

### Data extraction from John Guerrero's PPTX

The file `Presentacion_TPC_Barranquilla_2026.pptx` contains the TPC sector's official diagnostic. Extract via `python-pptx`:

```python
from pptx import Presentation
prs = Presentation(path)
for slide in prs.slides:
    for shape in slide.shapes:
        if shape.has_text_frame:
            for para in shape.text_frame.paragraphs:
                text = para.text.strip()
                if text: print(text)
```

Key slides and data to extract:

| Slide | Title | Key data for presentation |
|---|---|---|
| 2 | La crisis en cifras | -20% TPC, +4,000 motocarros, +18% motos, $0 FET/FQ |
| 4 | Transporte ilegal | Sin SOAT/sin revisión/sin habilitación. $0 aporte al sistema formal |
| 6 | Transmetro + FET | Doble sangría al sistema |
| 9-10 | Recomendaciones / 3 fases | Plan de acción |
| 11 | Solicitud formal | 5 medidas a la Alcaldía |

### Key argument: motos eléctricas y motocarros no pagan FET/FQ

This is the **strongest argument** for the hypothesis of demand dispersion:

- **+4,000** motocarros ilegales estimados (AMB 2024) — verified figure from John Guerrero's official presentation
- **$0** aportan al FET, al FQ, ni al sistema formal de transporte
- **Sin SOAT, sin revisión técnico-mecánica, sin habilitación de ruta**
- Captan usuarios con tarifas artificialmente bajas porque evaden **todos** los costos regulatorios

**Where to insert in the presentation:**
- **Slide 2 (Demanda fuera del sistema)**: Replace generic numbers with +4,000 motocarros and $0 FET/FQ. Integrate the insight AS THE PRIMARY CONTENT — use a dark hero panel at the top of the left column with the key message. Add the supporting data (dato clave) in a secondary panel below it. NO separate callout footnotes.
- **Slide 10 (Factores de la crisis)**: Add as a bullet under "Hechos verificados"
- **Slide 9 (La crisis en cifras)**: Already has the moto/motocarro data from AMB series

### Data verification: chart vs. source cross-reference

**Critical pattern:** When a presentation contains a chart, ALWAYS verify the first 2-3 data points against the source PDF. In one session, the chart showed Abril at ~$1,400M and Mayo at ~$1,500M, but the actual PDF data was $2,509M and $981M — the chart used smoothed averages instead of actual data.

**Workflow:**
1. Extract raw data from the source PDF via `pdftotext`
2. Compare 2+ data points from the chart against the source
3. If they differ, the chart is using estimates — fix to use actuals for past months
4. Label estimates clearly for future months

### Presentation text: integrate insights as main content, not callout footnotes

The user corrected two presentation patterns:

**1. No separate callout panels at the bottom:** Don't put key insights in separate panels below the main content with tiny font. Integrate them as part of the main slide flow — for example, turn the insight into a dark hero section at the top of the left column.

✅ Right: Insight is the PRIMARY content of the slide, with supporting data around it.
❌ Wrong: Two small panels at the bottom with `font-size: var(--small-size)`.

**2. Font sizes (commit to memory from session correction):** Base `--body-size` minimum `clamp(0.9rem, 1.5vw, 1.125rem)` and `--small-size` minimum `clamp(0.8rem, 1.1vw, 0.9rem)`. On narrow viewports (≤900px), boost body to at least `clamp(0.9rem, 2.5vw, 1rem)` and small to `clamp(0.8rem, 2vw, 0.85rem)`. On very narrow (≤600px), body `clamp(0.95rem, 3vw, 1.05rem)` and small `clamp(0.85rem, 2.5vw, 0.9rem)`. Never use `--small-size` for key arguments or insights.

**3. Three-scenario card pattern (approved design):** When presenting stress-test scenarios (Hoy / −5% / −10%), use this structure per card:

```
┌─ Número de círculo (①②③) + Título del escenario ─┐
│                                                     │
│   CIFRA GRANDE ($490M)    etiqueta (déficit mensual) │
│                                                     │
│   Descripción: "Reserva alcanza/alcanza 19 meses"    │
│   ──── línea divisoria ────                          │
│   Faltan CIFRA DESTACADA ($39M)                      │
└─────────────────────────────────────────────────────┘
```

- Each card gets a gradient background (green/dorado/red tones matching severity)
- A numbered circle (width:28px, border-radius:999px) in the accent color
- Title in `clamp(1rem, 1.8vw, 1.3rem)`
- Main figure in `clamp(1.5rem, 2.8vw, 2.2rem)` using `var(--font-display)` + color matching the severity
- Description line with `clamp(0.85rem, 1.2vw, 1rem)`
- Final result separated by a border-top line
- Padding: 0.8rem 1rem
- Gap between cards: 0.7rem
- Layout: `grid-template-columns: 1fr 1fr 1fr` for desktop, `1fr` on mobile (via !important breakpoint)

### PDF download button with print support

Two approaches, in order of reliability:

**A. Playwright server-side PDF (recommended for final deliverables)**

Use headless Chromium via Playwright to render the full page and export directly to PDF. This avoids all print-CSS issues (canvas blanking, `100vh`, flex layout breakage, text color legibility). See `netlify-static-deployment/references/playwright-pdf-generation.md` for the complete script and deployment workflow.

After generating the PDF, copy it to the Netlify public directory and change the button's `onclick` to `window.location.href='filename.pdf'` for one-click download.

**B. `window.print()` CSS approach (fallback for quick iterations)**

Add a floating "Descargar PDF" button that uses `window.print()` to open the browser's save-as-PDF dialog:

1. **Button HTML** — Fixed bottom-right, rounded pill, blue (#195d93), SVG download icon.
2. **Print CSS** — `@page { size: landscape; }`, `-webkit-print-color-adjust: exact` on all elements.
3. **Slide layout in print** — Override `height: 100vh` to `height: auto; min-height: 100vh`, `overflow: visible !important` on `.slide` and `.slide-content`, `max-height: none` on `.slide-content`, `scroll-snap-align: none`.
4. **Canvas fix for Chart.js** — Chart.js renders on `<canvas>` which often doesn't print. Add JavaScript that converts every canvas to a PNG image (`canvas.toDataURL('image/png')` → `<img>`) just before the print dialog opens, then restores the canvases after. Hook into `window.matchMedia('print')`, `beforeprint`/`afterprint` events, and the button's onclick directly.
5. **Chart containers** — Set `break-inside: avoid` so they don't split across pages.

### Content writing rules (user preference)

**1. Be concise — "frases breves pero que den el mensaje."** No verbose explanations. Each sentence should carry one point. If it can be said in 8 words instead of 15, do it. The user has explicitly repeated this preference multiple times — treat it as a hard rule, not a suggestion.

✅ Right: "El FQ no alcanza para pagar la renovación. Sin recursos, los pagos a Superpolo se frenan."
❌ Wrong: "La caída del FQ pone en riesgo directo la continuidad del proyecto de renovación de flota. Si el fondo no genera los recursos comprometidos, el plan de pagos a Superpolo y la reposición de buses pueden frenarse, dejando al sistema sin la mejora prometida."

**2. No repeat data across slides.** Each slide tells its own part of the story. If data like `+4.000 motocarros`, `$0 aporte`, or `-33.1%` appears in one slide's core argument, do NOT list the same exact numbers in other slides. Reference the concept, not the digits. The user gave direct feedback: "no repitas estos datos" — this means removing redundant bullet points from other slides, even if the context is slightly different.

**3. Add risk/contextual messaging.** Don't just show the numbers — explain what they mean for the business:
- FQ impact → "esto pone en riesgo el proyecto de renovación de flota"
- Reserve depletion → "las empresas TPC asumirían la diferencia entre recaudo y cuota, estresando su flujo de caja"

**4. Alert positioning preference.** When adding warning/alert content on a slide that has a chart at top and data cards below:
- Place the alert panel **immediately below the chart** (not at the bottom of the slide)
- Use a **white box** (`background: #fff; border: 1px solid var(--border)`) — not colored/shaded panels
- Font size should be **larger than small-size**: at least `clamp(0.85rem, 1.4vw, 1rem)`

### Responsive presentation: inline grid overrides

The HTML presentation uses inline `style="grid-template-columns:..."` on `.two-col` and `.metric-grid` elements. These INLINE STYLES override the `@media (max-width: 900px)` breakpoint that stacks columns on mobile. Fix:

```css
@media (max-width: 900px) {
  :root {
    --body-size: clamp(0.9rem, 2.5vw, 1rem);
    --small-size: clamp(0.8rem, 2vw, 0.85rem);
  }
  .masthead, .two-col, .timeline {
    grid-template-columns: 1fr !important;
  }
  .metric-grid {
    grid-template-columns: 1fr !important;
  }
  .fq-runway {
    grid-template-columns: 1fr 1fr !important;
  }
  /* Charts need smaller max-height on mobile */
  #chartFQ2025, #chartReserva {
    max-height: 25vh !important;
  }
}
@media (max-width: 600px) {
  :root {
    --body-size: clamp(0.95rem, 3vw, 1.05rem);
    --small-size: clamp(0.85rem, 2.5vw, 0.9rem);
  }
}
```

### Netlify: deploying to an existing specific site

After `netlify deploy --prod` creates a random new site (auto-created when no site is linked), you must:

1. Find existing site ID: `netlify sites:list | grep "site-name"`
2. Unlink: `netlify unlink`
3. Link to existing: `netlify link --id <site-uuid>`
4. Deploy: `netlify deploy --prod --dir=public`

### Git workflow for presentation updates

```bash
git checkout -b feat/description-of-change
git add presentacion_estado_actual_sistema_2026.html public/index.html README.md
git commit -m "feat: description"
git push origin feat/description-of-change

# Deploy to specific Netlify site
netlify unlink  # if linked to wrong site
netlify link --id <site-uuid>
netlify deploy --prod --dir=public
```

To find the site ID: `netlify sites:list | grep "site-name"` — the ID is the UUID.

## Anti-patterns

- ❌ Asking the user to run SQL in Supabase Dashboard — do everything via REST API
- ❌ Assuming propietario.code matches codigo_vehiculo — it does not
- ❌ Single 1,000-record query when table has more data — always paginate
- ❌ Copying credentials into conversation — read from paste file or .env
- ❌ Using `in.(plate1,plate2,...)` for many plates — query one plate at a time with `eq.`
- ❌ Showing code or query details in Telegram-formatted text reports
- ❌ Stopping at the known set of propietarios — always use `ilike.*SEARCH*` to discover additional family members
- ❌ Padding numbers with spaces in Telegram table cells — let Markdown rendering handle alignment
