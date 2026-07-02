# Flota nueva — Yutong, Fotón, Chevrolet MS

## Mapeo oficial (de foto del listado maestro, 2026-06-25)

| Interno | Placa | Marca |
|---|---|---:|
| 1055 | LO698 | Yutong |
| 1056 | LO699 | Yutong |
| 1057 | LO700 | Yutong |
| 1022 | LO712 | Fotón |
| 1023 | LO713 | Fotón |
| 1024 | LO714 | Fotón |
| 1025 | LO715 | Fotón |
| 1026 | LO716 | Fotón |
| 1028 | LO711 | Fotón |
| 1027 | LO845 | Chevrolet MS |
| 1034 | LO844 | Chevrolet MS |
| 1035 | LO846 | Chevrolet MS |
| 1036 | LO841 | Chevrolet MS |
| 1038 | LO843 | Chevrolet MS |

⚠️ **En BD las placas están con prefijo `LJO`** (LJO698, LJO699, etc.). Buscar con `LJO`.
⚠️ **LO842 (Interno 1037) no registra viajes aún.**
⚠️ **Siempre preguntar antes de asignar marcas a placas**, el usuario tiene el listado fuente y lo corrige si se asume.

## Estructura de consultas semanales

### Obtener timbradas por semana

```python
weeks = [
    ("Abr S4","2026-04-20","2026-04-26"),
    ("May S1","2026-04-27","2026-05-03"),
    ... hasta Jun S5
]

for placa in plates:
    r = requests.get(f"{url}/rest/v1/viajes_recaudados", headers=h,
        params={"placa": f"eq.{placa}", "select": "fecha_viaje,timbradas",
                "limit": 2000})
    for w_label, w_start, w_end in weeks:
        w_t = int(sum(float(v.get('timbradas',0) or 0) for v in data 
                      if w_start <= (v.get('fecha_viaje','')[:10]) <= w_end))
```

### Tipologías de pérdida

Columna `tipologia` en `viajes_perdidos`. Valores comunes: CONDUCTOR, VEHICULO, N/A, OTROS.
Columna `novedad` contiene la causa: TALLER, AUSENCIA CONDUCTOR, FUERA FRECUENCIA, APROVECHAMIENTO, VARADO EN RUTA, ACCIDENTE TRANSITO, etc.

### Recurrencia

```python
dias_activos = set(v['fecha_viaje'][:10] for v in data if v.get('fecha_viaje'))
fechas = sorted(dias_activos)
d1 = fechas[0]; d2 = fechas[-1]
delta = (d2 - d1).days + 1
recurrencia = len(dias_activos) / delta * 100
dias_por_semana = len(dias_activos) / (delta / 7)
```

### Promedio REAL por bus activo por mes

**NO dividir total del grupo / total buses.** Algunos entran después:

```python
group_monthly_active[m] = sum(1 for placa in plates if bus_monthly[placa].get(m,0) > 0)
group_monthly_total[m] = sum(bus_monthly[placa].get(m,0) for placa in plates)
promedio_real = group_monthly_total[m] / group_monthly_active[m]
```

## Foton vs Yutong — Comparativa de rendimiento (reunión 2026-06-04)

De reunión de afiliados del 2026-06-04:

| Modelo | Timbradas promedio | Consumo combustible | Observaciones |
|--------|:------------------:|:-------------------:|---------------|
| **Foton** | **96,3** | +27% vs convencional (Euro 6) | Problemas de calor (31-32°C) |
| **Yutong** | **96,3** | +27% vs convencional (Euro 6) | Mejor hermeticidad, más fresco. Mayor impacto visual |
| Convencionales | 72 | — | — |

- Ambos modelos tienen **+29% de productividad** vs convencionales (96,3 vs 72).
- La urea cuesta ~ **$80.000 el galón** (compartida entre ambos modelos).
- Ganancia neta del **+2%** (29% más timbradas − 27% más combustible).
- Decisión pendiente: opción de **10 Foton** vs **6 Yutong**.

### Estado actual de flota (2026-07-02)

| Marca | En operación | Target | Faltan |
|------|:------------:|:-----:|:-----:|
| Foton | 5 | 11 | 6 |
| Yutong | 3 | 11 | 1 |
| **Total** | **8** | **22** | **7** |

### Archivos fuente

- Obsidian: `empresas/la-carolina/Compra de buses/compra-buses/flota-foton-yutong-placas-internos.md`
- GitHub: `metrocaribe/public/data/flota-foton-yutong-placas-internos.md`

### Resultados clave (Junio 2026, todos activos)

| Tipología | Buses | Timb/bus/mes (real) | Recurrencia | vs Fotón |
|---|---|---|---|---|
| Yutong 🟢 | 3 | 5,965 | 85.7% | −2% |
| Fotón 🔵 | 6 | 6,116 | 87.7% | — |
| Chevrolet MS ⚪ | 5 | 3,913 | 87.2% | −36% |

### Proyección Jul-Dic

Factores de estacionalidad y promedio semanal base (Jun S2-S4):
- Yutong: 5,194 timb/sem (3 buses)
- Fotón: 10,630 timb/sem (6 buses)
- Chevrolet: 2,288 timb/sem (3 activas)
- Total proyectado Jul-Dic: ~451K timbradas / ~$130M
