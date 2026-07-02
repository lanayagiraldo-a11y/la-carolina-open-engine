# GESTIVO API — Esquema completo

> Descubierto: 2026-06-26 vía endpoint `/api/external/v1/schema`
> Base URL: `https://saas-six-vert.vercel.app`

## Conexión

```python
import urllib.request, json, ssl

KEY = "<api-key>"  # almacenada en sesiones previas de Hermes
BASE = "https://saas-six-vert.vercel.app"

def gestivo_get(resource, filters=None, limit=50):
    """Consulta GET a un recurso GESTIVO."""
    params = {"resource": resource, "limit": limit}
    if filters:
        params["filters"] = json.dumps(filters)
    url = f"{BASE}/api/external/v1/query?{urllib.parse.urlencode(params)}"
    req = urllib.request.Request(url, headers={"x-api-key": KEY})
    ctx = ssl.create_default_context()
    resp = urllib.request.urlopen(req, timeout=15, context=ctx)
    return json.loads(resp.read().decode())

def gestivo_query(resource, filters=None, limit=50):
    """Consulta POST (recomendada para queries complejas)."""
    body = {"resource": resource, "limit": limit}
    if filters:
        body["filters"] = filters  # [{ "column": "x", "op": "eq", "value": "y" }]
    data = json.dumps(body).encode()
    req = urllib.request.Request(f"{BASE}/api/external/v1/query",
        data=data, headers={"x-api-key": KEY, "Content-Type": "application/json"})
    ctx = ssl.create_default_context()
    resp = urllib.request.urlopen(req, timeout=15, context=ctx)
    return json.loads(resp.read().decode())
```

## Recursos disponibles (19)

### 🚌 Rotación (conductores + operaciones)

| Recurso | Columnas clave | Uso |
|---|---|---|
| `conductores_con_grupo` | id, nombre, cedula, estado, antiguedad, grupo, fecha_ingreso | Maestro conductores con antigüedad |
| `cierres_diarios` | id, fecha, viajes, timbradas, cumplimiento_pct, recaudo | Cierres diarios por ruta/bus. ⚠️ **Datos incompletos**: NO todas las fechas están cargadas. Solo ciertos días sueltos (ej: 9-11 Jun, 19-22 Jun). No usar como fuente única para totales semanales — complementar con Supabase `viajes_recaudados`. |
| `viajes_perdidos` | id, fecha, bus, ruta, turno, novedad, tipologia, detalle, conductor | Viajes perdidos con tipología |

### 💥 Accidentabilidad

| Recurso | Columnas clave | Uso |
|---|---|---|
| `accidentes` | id, fecha, bus, conductor, tipo, descripcion, danos | Reportes de accidentes |
| `accidente_evaluaciones` | accidente_id, evaluador, riesgo, fecha_eval | Evaluaciones de riesgo post-accidente |
| `accidente_vehiculos` | accidente_id, placa, tipo_vehiculo, danos_reportados | Vehículos involucrados en accidentes |

### 👷 RRHH

| Recurso | Columnas clave | Uso |
|---|---|---|
| `incentivos` | id, conductor_id, mes, monto, concepto | Pagos de incentivos |
| `candidates` | id, nombre, cedula, estado, fecha_postulacion, medio | Candidatos reclutamiento |
| `vacancies` | id, cargo, estado, fecha_apertura, fecha_cierre | Vacantes |
| `employees` | id, nombre, cargo, departamento, fecha_ingreso, estado | Maestro de empleados |
| `familia` | id, empleado_id, nombre, parentesco, edad | Dependientes |
| `document_categories` | id, nombre, descripcion | Categorías documentales |

### 📢 Campañas

| Recurso | Columnas clave | Uso |
|---|---|---|
| `meta_campaigns` | id, name, status, objective, budget, start_date, end_date | Campañas Meta Ads |
| `meta_spend_daily` | campaign_id, date, spend, impressions, clicks, ctr | Gasto diario Meta Ads |
| `pipeline_stages` | id, nombre, orden | Etapas del pipeline reclutamiento |
| `departments` | id, nombre, jefe | Departamentos |
| `user_types` | id, nombre, permisos | Tipos de usuario |

### 🛠️ Sistema

| Recurso | Uso |
|---|---|
| `gema_sync_state` | Estado de sincronización con GEMA |

## Patrones de consulta

### Filtros por fecha
```python
filters = [
    {"column": "fecha", "op": "gte", "value": "2026-06-01"},
    {"column": "fecha", "op": "lte", "value": "2026-06-30"}
]
```

### Filtros por estado
```python
filters = [{"column": "estado", "op": "eq", "value": "ACTIVO"}]
```

### Múltiples condiciones (AND)
```python
filters = [
    {"column": "estado", "op": "eq", "value": "ACTIVO"},
    {"column": "grupo", "op": "eq", "value": "Rueda"}
]
```

## Diferencia clave vs Supabase

| Aspecto | GESTIVO | Supabase #1 |
|---|---|---|
| Accidentes | ✅ Tabla dedicada con evaluaciones | ❌ Solo viajes_perdidos (ACCIDENTE TRANSITO) |
| Campañas Meta | ✅ Tablas dedicadas | ❌ No disponible |
| Reclutamiento | ✅ candidates + vacancies | ❌ Solo SharePoint |
| Viajes | ✅ viajes_perdidos | ✅ viajes_recaudados + viajes_perdidos |
| Frescura de datos | En tiempo real (cerebro) | Snapshot periódico |
