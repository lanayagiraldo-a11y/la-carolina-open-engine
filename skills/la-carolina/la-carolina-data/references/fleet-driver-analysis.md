# Análisis de flota — Conductores, clasificación y reclutamiento

## Fuentes

- **Supabase** (`viajes_recaudados`, `viajes_perdidos`, `conductores`, `incentivos`, `ausentismo`, `propietarios`)
- **Reclutamiento GH**: CSV en `obsidian-vault/empresas/la-carolina/dashboard-redes-ads-reclutamiento/data/procesos_contratacion_raw.csv`

## Mapeo placa → número interno

El `codigo_vehiculo` en `viajes_recaudados` contiene el número interno del bus:

```python
r = requests.get(f"{url}/rest/v1/viajes_recaudados", headers=h,
    params={"placa": f"eq.{placa}", "select": "codigo_vehiculo", 
            "fecha_viaje": "gte.2026-06-01", "limit": 1})
interno = r.json()[0].get('codigo_vehiculo', '?')
```

## Clasificación de conductores

Siempre clasificar a los conductores en 3 categorías cuando se reporte el total único, para evitar dar una impresión inflada del equipo:

| Categoría | Criterio | ~% viajes |
|---|---|---|
| 🟢 **Fijos** | ≥100 viajes O (≥3 meses activos y ≥15 viajes/mes promedio) | ~90% |
| 🟡 **Semifijos** | 20-99 viajes O ≥2 meses activos | ~8% |
| 🔴 **Relevos** | <20 viajes Y <2 meses activos | ~2% |

Implementación:

```python
if viajes >= 100 or (meses >= 3 and viajes_por_mes_avg >= 15):
    categoria = "Fijo"
elif viajes >= 20 or meses >= 2:
    categoria = "Semifijo"
else:
    categoria = "Relevo"
```

Adicionalmente, identificar:

- **Conductores cross-bus**: los que manejaron 2+ buses distintos (relevos compartidos)
- **Conductores cross-propietario**: los que manejaron para 2+ afiliados Rueda (pool familiar)

## Cruce con reclutamiento

Para identificar el medio de postulación de cada conductor de flota vs. base GH:

1. Obtener todos los conductores únicos de la flota desde `viajes_recaudados`
2. Cargar CSV de reclutamiento (columna `NOMBRE`, `MEDIO DE POSTULACION`, `ESTADO DEL PROCESO`)
3. Matching por nombre normalizado (mayúsculas, sin signos, al menos 2 palabras significativas en común)
4. Para cada conductor, tomar el registro más reciente (mayor `FECHA DE CREACION`)

```python
def normalize_name(n):
    n = n.upper().strip()
    n = re.sub(r'[^\w\s]', ' ', n)
    n = re.sub(r'\s+', ' ', n)
    return n.strip()

rec_words = set(w for w in normalize_name(rec_name).split() if len(w) > 2)
rueda_words = set(w for w in normalize_name(rueda_name).split() if len(w) > 2)
overlap = rec_words & rueda_words
if len(overlap) >= 2:  # match!
```

## KPIs de reclutamiento por flota

- `% encontrados en base GH`: cobertura de la base de datos
- `% contratados formalmente`: los que tienen estado CONTRATADO
- `Fuentes`: distribución por MEDIO DE POSTULACION
- `Sin incentivo`: top productores que no aparecen en tabla `incentivos`

## Recurrencia de operación por bus

Mide qué tan seguido opera un bus desde su entrada:

```python
dias_activos = set(v['fecha_viaje'][:10] for v in data if v.get('fecha_viaje'))
fechas = sorted(dias_activos)
delta_dias = (datetime.date.fromisoformat(fechas[-1]) - datetime.date.fromisoformat(fechas[0])).days + 1
recurrencia = len(dias_activos) / delta_dias * 100
dias_por_semana = len(dias_activos) / (delta_dias / 7)
```

Rangos de interpretación:
- 95-100%: Diario (opera casi todos los días)
- 80-94%: Casi diario ✅ (estándar saludable)
- 60-79%: Regular 🟡 (tiene paradas frecuentes)
- <60%: Esporádico 🔴

## Estructura de informe HTML

Para reportes de flota con branding La Carolina:
- Fondo `#222222`, paneles `#2a2a2a`, dorado `#DCBE61`, teal `#3AAFA9`
- KPIs en grid, tablas con barras, gráficas Chart.js
- Secciones: global → por afiliado → top conductores → incentivos → reclutamiento → clasificación → ausentismo → insights
