---
name: la-carolina-data
version: 2.0.0
description: "Sistema central de datos de La Carolina: consulta GEMA MySQL, Supabase, Obsidian y OneDrive para análisis operativos, financieros y de personal."
tags: [la-carolina, supabase, gema, mysql, afiliados, datos, conductores]
---

# La Carolina — Sistema de Datos

Usa esta skill automáticamente cuando Liliana mencione La Carolina, afiliados, rutas, conductores, buses, datos operativos, ingresos, viajes, liquidaciones o cierres.

> ⚠️ **Estilo de entrega**: Cuando Liliana pida datos, haz la consulta directamente y entrega el resultado sin explicaciones previas, sin planear pasos ni preguntar si quiere que consultes. "Cada que te pida algo haces la consulta y me la entregas". Solo pregunta si hay ambigüedad en fechas o filtros.

## Fuentes activas

### 1. GEMA MySQL (operativa — prioridad para datos operativos/financieros)
- Host: `carolinainstance.c8twjwe0zioh.us-east-2.rds.amazonaws.com:3306`
- DB: `gema_cr`
- Usuario: `db_user_2_cr_ext` (READONLY — solo SELECT + EXECUTE)
- Conexión: `mysql -h <host> -u <user> -p'<pass>' -P 3306 gema_cr`
- ⚠️ CREDENTIALS EXPIRED — password from paste file no longer works. User needs to request renewed credentials from John Guerrero or Edith.
- Historic data (2024-2025) was in GEMA but is inaccessible until credentials are renewed.

**4 Vistas (maestros):**
| Vista | ~Registros | Contenido |
|---|---|---|
| `vst_ext_get_conductores` | 1,190 | Conductores activos/retirados con licencia, EPS, ARL, datos personales |
| `vst_ext_get_empleados` | 320 | Empleados no-conductores con cargo, departamento, grupo |
| `vst_ext_get_personal` | 1,648 | Personas (unifica conductores+empleados+propietarios) con flags |
| `vst_ext_get_propietarios` | 153 | Propietarios de buses con tipo, plazo pago, datos contacto |

**6 Stored Procedures (reportes por fecha):**
| Módulo | SP | Uso |
|---|---|---|
| Ingresos conductores | `pa_ext_get_IngresoConductorByFecha(fechaInicio, fechaFin)` | Liquidaciones, cierres, consolidados por conductor |
| Ingresos terceros | `pa_ext_get_IngresoTerceroByFecha(fechaInicio, fechaFin)` | Liquidaciones tercerizados |
| Viajes despacho | `pa_ext_get_ViajesByFecha(fechaInicio, fechaFin)` | Histórico despacho, viajes perdidos |
| Viajes recaudados | `pa_ext_get_ViajesRecaudadosByFecha(fechaInicio, fechaFin)` | Viajes efectivamente recaudados |
| Puntos virtuales | `pa_ext_get_PuntosVirtualesByFecha(fecha)` | Registradora + GPS |
| Velocidades | `pa_ext_get_VelocidadesByFecha(fecha)` | Datos de velocidad (extra) |

Formato reusable:
```bash
mysql -h carolinainstance.c8twjwe0zioh.us-east-2.rds.amazonaws.com -u db_user_2_cr_ext -p'<password>' -P 3306 gema_cr -N -e "CALL pa_ext_get_XByFecha('2026-06-01','2026-06-30');"
```
Las vistas se consultan con SELECT directo:
```bash
mysql -h ... -u ... -p'<pass>' -P 3306 gema_cr -N -e "SELECT * FROM vst_ext_get_conductores WHERE estado = 1 LIMIT 10;"
```

> 📁 **Detalle de columnas** en `references/gema-columnas.md`

### 1b. GESTIVO API (cerebro operacional — descubierto 2026-06-26)
- URL base: `https://saas-six-vert.vercel.app`
- Endpoints:
  - `GET /api/external/v1/schema` — lista todos los recursos disponibles
  - `POST /api/external/v1/query` — consulta SQL parametrizada
- Auth: API key `x-api-key` header
- Key almacenada en variable de entorno o sesiones previas de Hermes
- **19 recursos** agrupados en 4 módulos:

### Rotación (conductores + operaciones)
| Recurso | Descripción |
|---|---|
| `conductores_con_grupo` | Maestro de conductores con antigüedad |
| `cierres_diarios` | Cierres diarios: viajes, timbradas, cumplimiento |
| `viajes_perdidos` | Viajes perdidos con tipología y novedad |

### Accidentabilidad
| Recurso | Descripción |
|---|---|
| `accidentes` | Reportes de accidentes |
| `accidente_evaluaciones` | Evaluaciones de riesgo |
| `accidente_vehiculos` | Vehículos involucrados |

### RRHH
| Recurso | Descripción |
|---|---|
| `incentivos` | Pagos de incentivos |
| `candidates` / `vacancies` | Reclutamiento |
| `employees` | Maestro de empleados |
| `familia` | Dependientes |

### Campañas
| Recurso | Descripción |
|---|---|
| `meta_campaigns` | Campañas Meta Ads |
| `meta_spend_daily` | Gasto diario |

> ⚠️ GESTIVO es **complementario** a Supabase — tiene datos más frescos de accidentes y campañas Meta que Supabase no tiene. Priorizar GESTIVO para accidentalidad y campañas; Supabase para viajes y conductores.
>
> 📁 **Esquema completo** en `references/gestivo-api-schema.md`

### 2a. Supabase (analítica/dashboards)
- URL: https://exloqxhnzplovndcjale.supabase.co
- Conexión: API REST con anon key o service role key
  - **Anon key:** `sb_publishable_lJAS2jbplC_Pe6Q99Jxgbg_qX-N2L9r` (read-only, funciona para SELECT)
  - **Service role:** guardada en sesiones previas de Hermes (búscala con `session_search` si no está en env vars)
- 35 tablas, 128,004 registros
- Tablas clave: `viajes_recaudados`, `viajes_perdidos`, `conductores`, `ausentismo`, `cierres_diarios`, `incentivos`, `propietarios`, `employees`
- Fecha range: 2026-01-01 → present (NO 2025 data)

### 2b. Supabase #2 (conductores master list)
- URL: https://lqeddrpbwunzcyjxuiei.supabase.co
- Only 1 table: `conductores` (168 records, all active, placa_asignada all None)
- 1 empty table: `incentivos`
- Credentials provided by user in chat (2026-06-25), not in paste file
- NOT for operational analysis — only driver master list lookups

### 2c. Reclutamiento (SharePoint/CSV)
- File: `Procesos de reclutamiento.xlsx` on SharePoint (datastewarlacarolina.sharepoint.com)
- CSV local: `obsidian-vault/empresas/la-carolina/dashboard-redes-ads-reclutamiento/data/procesos_contratacion_raw.csv`
- 3,654 records with `MEDIO DE POSTULACION` column
- Key sources: WhatsApp, Computrabajo, Referido, Varylo, ManyChat
- Account: lanaya@lacarolina.com.co (Microsoft Graph)

### 3. OneDrive (hotmail personal — lanaya_giraldo@hotmail.com)
- Documentos de transporte, MTC, afiliados 2026

### 4. Obsidian
- Vault en `/root/obsidian-vault/`
- Notas de reuniones, operación, flota en `empresas/la-carolina/`

## Flujo de trabajo

1. **GESTIVO API** para accidentalidad y campañas Meta (datos más frescos que Supabase)
2. **GEMA MySQL** para datos operativos históricos (ingresos, viajes, puntos, conductores, velocidades) — credenciales vencidas
3. **Supabase** para analítica y dashboards consolidados (viajes, conductores, ausentismo)
4. **Obsidian** para contexto de reuniones y notas
5. **OneDrive** para documentos fuente (último recurso)

## Análisis semanal de timbradas — flujo recomendado

Consulta recurrente: Liliana pide timbradas (pasajeros transportados) de una semana.

**Flujo de 2 fuentes:**

1. **GESTIVO** → recurso `cierres_diarios` — trae desglose a nivel conductor (nombre, vehículo, ruta, timbradas individuales, promedios). Limitación: NO todas las fechas están cargadas (solo ciertos días sueltos).
2. **Supabase #1** → tabla `viajes_recaudados` — trae TODOS los días pero requiere paginación (>1000 registros/semana). Usar `select=fecha_viaje,timbradas,placa,ruta_programada` para tener datos de vehículo y ruta.

**Patrón de entrega:**
- Resumen diario (timbradas, viajes, promedio/viaje)
- Desglose por ruta (top rutas con %)
- Top 10-15 vehículos por timbradas
- ⚠️ Vehículos con menor promedio (min 10 viajes) — alertas de bajo rendimiento
- Comparación con semana anterior cuando sea relevante

## Reglas
- ❌ No inventar números — siempre consultar fuente real
- ✅ Mostrar fuente (GEMA / Supabase / Obsidian) y período exacto en cada respuesta
- ✅ Usar `-N` en mysql para output limpio (sin cabeceras)
- ✅ Si el dato requiere JOINs entre vistas, hacer subconsultas SELECT, no SPs
- ❌ No preguntar "¿quieres que consulte?" — solo consultar y entregar

## Flota nueva — Análisis semanal

⚠️ **Siempre verificar placas con fuente oficial** (foto del listado maestro, no asumir). El usuario tiene un listado de Interno→Placa→Marca. Usar ese listado como fuente de verdad.

**Mapeo correcto (actualizado 2026-07-02 desde imagen de hoja de cálculo):**

| Marca | Buses | Placas (BD: LJOxxx) | Internos |
|---|---|---|---|
| Yutong 🟢 | 3 | LO698, LO699, LO700 (BD: LJO698-700) | 1055, 1056, 1057 |
| Fotón 🔵 | 6 | LO711-LO716 (BD: LJO711-716) | 1022-1028 |
| Chevrolet MS ⚪ | 5 | LO841-LO846 (BD: LJO841-846) | 1027, 1034-1038 |
| Sin marca | 1 | LO715 (BD: LJO715) | 1025 |

⚠️ En BD las placas están como `LJOxxx` pero el listado oficial dice `LOxxx`. Buscar con prefijo `LJO`.
⚠️ **1025 (LO715)** aparece en el listado sin marca asignada — no asumir Foton aunque esté en el rango de internos.
⚠️ Archivos fuente de esta data:
- Obsidian: `empresas/la-carolina/Compra de buses/compra-buses/flota-foton-yutong-placas-internos.md`
- GitHub: `metrocaribe/public/data/flota-foton-yutong-placas-internos.md`

### Cálculo de promedio REAL por bus activo

**NO dividir total timbradas / total buses.** Algunos buses entraron después. Calcular:

```python
# Para cada mes, contar solo buses que tuvieron viajes ese mes
for m in meses:
    total_mes = sum(timbradas for buses activos ese mes)
    activos = count(buses con timbradas > 0 ese mes)
    promedio_real = total_mes / activos
```

### Recurrencia

Mide qué tan seguido opera un bus desde su entrada. Fórmula:
```
recurrencia = días_activos / días_desde_primera_vez * 100
```

### Proyección con estacionalidad

Factores típicos para transporte urbano en Barranquilla:
- Jul: 0.95 (recuperación post-junio)
- Ago: 0.85 (vacaciones)
- Sep: 0.95 (regreso a clases)
- Oct: 1.00 (normal)
- Nov: 1.05 (pre-navidad)
- Dic: 0.70 (navidad)

## OS La Carolina — Fuentes para integrar

Visión de Operating System que unifique:
1. Supabase #1 (viajes, timbradas, conductores, incentivos, ausentismo)
2. Supabase #2 (conductores master)
3. Reclutamiento (SharePoint CSV)
4. GEMA MySQL (histórico — credenciales vencidas)
5. Accidentalidad (pendiente — pedir link)
6. Gestión Humana (pendiente — pedir link)

## Referencias disponibles
- `references/gema-columnas.md` — columnas de vistas GEMA
- `references/fleet-driver-analysis.md` — clasificación, reclutamiento, conductores
- `references/fleet-new-buses.md` — análisis detallado flota nueva (Yutong/Fotón/Chevrolet)
- `references/supabase-rest-api-patterns.md` — conexión REST, SUM por período, agrupación semanal (day-by-day), tablas clave, y deploy Netlify de dashboards
