# Supabase Table Schemas — La Carolina Analítica

Discovered via REST API queries during fleet analysis sessions.
Credentials: `~/.hermes/pastes/paste_1_203014.txt`

## Tablas principales

### viajes_recaudados (~162K registros)
| Columna | Tipo | Notas |
|---|---|---|
| placa | text | WGA422, WGB055, etc. |
| conductor_nombre | text | Nombre en mayúscula sostenida |
| bruto | numeric | Ingreso bruto del viaje |
| neto | numeric | Ingreso neto |
| timbradas | numeric | Timbradas del viaje |
| pasaje | numeric | Pasaje |
| fecha_viaje | date | YYYY-MM-DD |
| ruta_programada | text | Código de ruta |
| ruta_reprogramada | text | Ruta real |
| propietario_nombre | text | Nombre del propietario (para JOIN!) |

| codigo_vehiculo | text | Número interno 3 dígitos (ej: 619 = WGA422). NO coincide con código de propietario (4 dígitos). |
| cedula_propietario | text | Cédula del dueño del bus |

**Join keys:**
- `propietario_nombre` (text match) — primary join between viajes_recaudados and propietarios
- `codigo_vehiculo` — 3-digit internal bus number. Query: `{"select": "codigo_vehiculo", "placa": f"eq.{placa}", "limit": 1}`
- NEVER join viajes_recaudados.codigo_vehiculo with propietarios.codigo — they are different numbering systems (3-digit vs 4-digit)

### viajes_perdidos (~40K registros)
| Columna | Tipo | Notas |
|---|---|---|
| placa | text | WGA422, etc. |
| novedad | text | Causa: TALLER, AUSENCIA CONDUCTOR, VARADO EN RUTA, etc. |
| detalle_novedad | text | Descripción libre. **ACCIDENTE TRANSITO**: "REPARACION DAÑOS ACCIDENTE" = día en taller por accidente. "CON RECAUDO" = leve, siguió operando. |
| fecha | date | YYYY-MM-DD |
| conductor_nombre | text | Conductor asignado |
| cedula_conductor | text | Cédula |
| ruta | text | Ruta |

### conductores (1,190 registros)
| Columna | Tipo | Notas |
|---|---|---|
| id | int8 | PK |
| cedula | text | Cédula única |
| nombre | text | Nombre completo |
| codigo | text | Código interno |
| estado | int4 | 1=activo, 0=inactivo |
| tipo_conductor | text | 'Propio' o 'Afiliado' |
| fecha_ingreso | date | Ingreso a la empresa |
| fecha_retiro | date | Retiro (si aplica) |
| eps | text | EPS |
| arl | text | ARL |
| licencia | text | Categoría licencia |
| venc_licencia | date | Vencimiento |
| celular | text | Teléfono |
| correo | text | Email |
| direccion | text | Dirección |

### incentivos (~400 registros)
| Columna | Tipo | Notas |
|---|---|---|
| id | int8 | PK |
| cedula | text | Cédula conductor |
| nombre | text | Nombre conductor |
| mes_entrega | text | Mes de entrega (ej: "2026-04") |
| periodo | text | Período que cubre |
| valor | numeric | Monto del incentivo |
| concepto | text | Razón |

**Critical for gap analysis:** Compare viajes_recaudados conductor names against incentivos.nombre to find high-volume drivers without benefits.

### ausentismo (~2K registros)
| Columna | Tipo | Notas |
|---|---|---|
| id | int8 | PK |
| cedula | text | Cédula conductor |
| nombre | text | Nombre conductor |
| genero | text | |
| edad | int4 | |
| antiguedad | int4 | Días de antigüedad |
| centro_trabajo | text | |
| departamento | text | |
| cargo | text | "Conductor" |
| indicador_prorroga | text | |
| dias_it_pagados | numeric | Días de incapacidad pagados |
| origen | text | Enfermedad general, laboral, etc. |

### propietarios (153 registros)
| Columna | Tipo | Notas |
|---|---|---|
| id | int8 | PK |
| cedula | text | Cédula |
| nombre | text | Nombre completo |
| codigo | int4 | Código (ej: 4190) — NO coincide con código de vehículo |
| direccion | text | Domicilio |
| telefono | text | |
| tipo | text | 'AFILIADO' o 'PROPIO' |
| plazo_pago | text | 'SEMANAL' |

## Join patterns

| Qué quieres | Cómo |
|---|---|
| Viajes por propietario | `viajes_recaudados.propietario_nombre ilike.*RUEDA*` |
| Perdidos por bus | `viajes_perdidos.placa = WGA422` |
| Incentivos de conductores de un afiliado | Match por nombre: `viajes_recaudados.conductor_nombre ∪ viajes_perdidos.conductor_nombre → incentivos.nombre` |
| Ausentismo de conductores de un bus | Match por nombre: `viajes_recaudados.conductor_nombre → ausentismo.nombre` |
| Conductores nuevos en 2026 | `viajes_recaudados` first `fecha_viaje` per conductor >= 2026-01-01 |

## Stale data warning

`viajes_recaudados` in Supabase may lag GEMA MySQL by days or weeks. Always note the period covered and cross-check with GEMA's `pa_ext_get_ViajesRecaudadosByFecha` when numbers seem low.
