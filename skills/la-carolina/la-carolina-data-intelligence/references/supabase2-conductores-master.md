# Supabase #2 — Conductores Master List

**URL:** `https://lqeddrpbwunzcyjxuiei.supabase.co`

## Propósito

Lista maestra de conductores — no contiene datos operacionales (no viajes, timbradas, recaudos, ni accidentes).

## Tablas

### conductores (168 registros, todos activos)

| Columna | Tipo | Notas |
|---|---|---|
| cedula | text | Cédula |
| nombre_completo | text | Nombre completo |
| placa_asignada | text | **Todos NULL** — no se ha asignado placa aún |
| activo | bool | Todos `true` |
| created_at | timestamptz | Fecha de creación (mayo-junio 2026) |
| fecha_reingreso | timestamptz | Todos NULL |

### incentivos (0 registros)

Tabla vacía — no usar.

## Cuándo usar

- Solo para consultar el listado maestro de conductores (cédula + nombre)
- NO para análisis operacional, timbradas, o reclutamiento

## Credenciales

Proporcionadas por Liliana en chat (2026-06-25). No están en el archivo de pastes.
