# Supabase REST API — Patrones de consulta para La Carolina

## Conexión básica

```python
import json, urllib.request, ssl

ctx = ssl.create_default_context()
url = 'https://exloqxhnzplovndcjale.supabase.co'
key = 'sb_publishable_lJAS2jbplC_Pe6Q99Jxgbg_qX-N2L9r'  # anon key = readonly

headers = {
    'apikey': key,
    'Authorization': f'Bearer {key}',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
}
```

> La **anon key** funciona para lecturas (SELECT). Si necesitas escribir, usa la service role key `eyJhbG...P2V0` guardada en sesiones previas de Hermes.

## Buscar credenciales cuando no están en env vars

Si `SUPABASE_LC_KEY` no está configurada en el entorno, buscar con:

```
session_search(query="exloqxhnzplovndcjale service role key", limit=5)
```

Luego inspeccionar los resultados para encontrar el anon key o service role key que el usuario compartió en conversaciones anteriores.

## Obtener total agregado (SUM)

```python
# Sumar todas las timbradas de un período
q = f'{url}/rest/v1/viajes_recaudados?select=timbradas.sum()&fecha_viaje=gte.2026-05-01&fecha_viaje=lte.2026-06-30'
req = urllib.request.Request(q, headers=headers)
with urllib.request.urlopen(req, context=ctx, timeout=30) as resp:
    data = json.loads(resp.read())
    total = data[0]['sum']  # {"sum": 1492672}
```

## Consulta batch con paginación (RECOMENDADA para semanas)

PostgREST limita a **1000 registros por respuesta** por defecto. Para rangos de 1 semana (~1800-2000 registros en `viajes_recaudados`), usa paginación con `Range` header en vez de consultar día por día — es más rápido (2 requests vs 5-7) y trae columnas adicionales (placa, ruta) que permiten desgloses más ricos.

```python
import json, urllib.request, ssl
from collections import defaultdict

ctx = ssl.create_default_context()
url = 'https://exloqxhnzplovndcjale.supabase.co/rest/v1/viajes_recaudados'
headers = {
    'apikey': 'sb_publishable_lJAS2jbplC_Pe6Q99Jxgbg_qX-N2L9r',
    'Authorization': 'Bearer sb_publishable_lJAS2jbplC_Pe6Q99Jxgbg_qX-N2L9r',
    'Accept': 'application/json'
}

# Calcular total de registros primero (sin Range, solo count)
count_req = urllib.request.Request(
    f'{url}?select=fecha_viaje&fecha_viaje=gte.2026-06-22&fecha_viaje=lte.2026-06-26',
    headers={**headers, 'Prefer': 'count=exact', 'Range': '0-0'}
)
with urllib.request.urlopen(count_req, context=ctx, timeout=15) as resp:
    total_count = int(resp.headers.get('content-range', '0-0/0').split('/')[1])

# Paginar: 0-999, 1000-1999, etc.
all_data = []
for offset in range(0, total_count, 1000):
    h2 = dict(headers)
    h2['Range'] = f'{offset}-{offset+999}'
    q = f'{url}?select=fecha_viaje,timbradas,placa,ruta_programada&fecha_viaje=gte.2026-06-22&fecha_viaje=lte.2026-06-26'
    req = urllib.request.Request(q, headers=h2)
    with urllib.request.urlopen(req, context=ctx, timeout=30) as resp:
        all_data.extend(json.loads(resp.read()))

# Agrupar por día
by_day = defaultdict(lambda: {'timbradas': 0, 'count': 0})
for r in all_data:
    d = r.get('fecha_viaje','')[:10]
    t = r.get('timbradas', 0) or 0
    by_day[d]['timbradas'] += t
    by_day[d]['count'] += 1

# Desglose por placa
by_placa = defaultdict(lambda: {'timbradas': 0, 'viajes': 0, 'dias': set()})
for r in all_data:
    p = r.get('placa','Sin placa')
    t = r.get('timbradas',0) or 0
    d = r.get('fecha_viaje','')[:10]
    by_placa[p]['timbradas'] += t
    by_placa[p]['viajes'] += 1
    by_placa[p]['dias'].add(d)

# Desglose por ruta
by_ruta = defaultdict(lambda: {'timbradas': 0, 'count': 0})
for r in all_data:
    ruta = r.get('ruta_programada', 'Sin ruta')
    t = r.get('timbradas', 0) or 0
    by_ruta[ruta]['timbradas'] += t
    by_ruta[ruta]['count'] += 1
```

**Ventajas sobre el approach día por día:**
- 2 requests en vez de 5-7 para una semana
- Trae `placa` y `ruta_programada` para desgloses detallados
- Más fácil integrar comparación semana-vs-semana

## Agrupar por semana (día por día + Python, alternativa legacy)

Solo usar cuando la paginación batch falle o para rangos muy largos (>1 mes).

```python
import datetime
from collections import defaultdict

results = []
current = datetime.date(2026, 5, 1)
end = datetime.date(2026, 7, 1)

while current < end:
    day_str = current.isoformat()
    q = f'{url}/rest/v1/viajes_recaudados?select=timbradas&fecha_viaje=eq.{day_str}'
    req = urllib.request.Request(q, headers=headers)
    with urllib.request.urlopen(req, context=ctx, timeout=30) as resp:
        rows = json.loads(resp.read())
        timb_total = sum(r['timbradas'] for r in rows if r['timbradas'] is not None)
        trip_count = len(rows)
        results.append((day_str, trip_count, timb_total))
    current += datetime.timedelta(days=1)

weeks = defaultdict(lambda: {'trips': 0, 'timbradas': 0})
for day_str, trips, timb in results:
    d = datetime.date.fromisoformat(day_str)
    iso_year, iso_week, _ = d.isocalendar()
    week_key = f'{iso_year}-W{iso_week:02d}'
    weeks[week_key]['trips'] += trips
    weeks[week_key]['timbradas'] += timb
```

**Limitación:** ~62 requests para 2 meses (~30-60s). Preferir batch paginado para semanas.

## Tablas clave en Supabase #1

| Tabla | Registros | Columnas clave | Descripción |
|---|---|---|---|
| `viajes_recaudados` | 60K+ | `fecha_viaje`, `timbradas`, `bruto`, `placa`, `propietario_nombre`, `ruta_programada` | Viajes con recaudo (2026) |
| `viajes_perdidos` | 17K+ | `fecha_viaje`, `placa`, `novedad`, `causal` | Viajes perdidos con causa |
| `conductores` | 1,190 | `cedula`, `nombre`, `activo` | Maestro de conductores |
| `ausentismo` | 472 | `fecha_inicio`, `fecha_fin`, `conductor_id`, `diagnostico` | Incapacidades y ausencias |
| `incentivos` | Variable | `conductor_id`, `mes`, `monto` | Bonos e incentivos |
| `propietarios` | 153 | `cedula`, `nombre`, `tipo` | Dueños de buses |

## Convenciones

- Las **placas** en BD tienen prefijo `LJO` (ej. `LJO698`) pero el listado oficial usa `LO` (ej. `LO698`). Buscar con prefijo `LJO` y presentar como `LO`.
- La columna `timbradas` = número de pasajeros/timbres por viaje individual.
- La columna `bruto` = ingreso bruto del viaje en COP.
- `fecha_viaje` es DATE (formato YYYY-MM-DD).
- Los datos disponibles van de 2026-01-01 al presente. **No hay datos 2025 en Supabase.**

## Deploy de dashboard a Netlify

```bash
cd /root/proyectos/la-carolina-data/mtc-analisis-de-base-de-datos/dashboard-rueda/deploy-flota-nueva
npx netlify deploy --prod --dir=.
```

URL deployada: `https://nexus-os-liliana.netlify.app`
