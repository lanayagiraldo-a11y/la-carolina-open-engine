# Supabase La Carolina — Columnas de tablas

Proyecto: `exloqxhnzplovndcjale.supabase.co`
Tablas: 35 | Registros: ~128,004

## Tablas principales

### viajes_recaudados (60,822 registros)
`id, numero, fecha_viaje, hora_despacho, hora_llegada, codigo_vehiculo, placa, conductor_nombre, codigo_conductor, cedula_conductor, viaje, inicial, final, descuento, timbradas, timbradas_real, bruto, anticipo, factura, ahorro, neto, fecha_recaudo, is_extemporaneo, cajero, pasaje, propietario_nombre, cedula_propietario, estado, novedad, ruta_programada, ruta_reprogramada, is_viaje_contable, source_file, created_at`

### viajes_perdidos (17,723 registros)
`id, cedula_conductor, tipologia, novedad, detalle_novedad, fecha, despacho, tipo_propietario, vehiculo, placa, conductor_nombre, turno, viaje, ruta, planillero, periodo, quincena, source_file, created_at, origen`

### conductores (1,192 registros)
`id, cedula, nombre, codigo, correo, direccion, celular, telefono, tipo_conductor, licencia, venc_licencia, venc_contrato, fecha_ingreso, fecha_retiro, experiencia, fecha_nacimiento, observacion, eps, arl, pension, compensacion, tipo_sangre, nivel_educativo, num_hijos, estado_civil, reubicado, estado, created_at, updated_at, fecha_reingreso`

### propietarios (153 registros)
`id, cedula, codigo, nombre, tipo_identificacion, tipo_propietario, plazo_pago, direccion, telefono, celular, correo, estado, updated_at`

### ausentismo (472 registros)
`id, cedula, consecutivo_incapacidad, nombre, genero, edad, antiguedad, vinculacion, centro_trabajo, departamento, area, cargo, indicador_prorroga, dias_it_pagados, origen, fecha_inicio, fecha_fin, mes_inicio, cie10, diagnostico, soat, grd, dia_ocurrencia, eps, ips, profesional_responsable, tipo_conductor, estado, source_file, created_at`

### incentivos (82 registros)
`id, cedula, nombre, mes_entrega, periodo, valor, concepto, source_file, created_at`

### cierres_diarios (23,503 registros)
`id, cod_conductor, conductor_nombre, fecha, tipo_cierre, ruta, grupo_liquidacion, vehiculo, viajes, timbradas, diff_tim, prom_tim, pct_indiv, pct_grupo, pct_total, tim_grupo, viajes_grupo, prom_grupo, source_file, created_at, origen`

## Reglas de vinculación entre tablas

| Origen | Destino | Columna | Nota |
|---|---|---|---|
| viajes_recaudados | conductores | viajes_recaudados.cedula_conductor → conductores.cedula | |
| viajes_recaudados | propietarios | viajes_recaudados.cedula_propietario → propietarios.cedula | |
| viajes_perdidos | viajes_recaudados | viajes_perdidos.placa → viajes_recaudados.placa | |
| viajes_perdidos | conductores | viajes_perdidos.cedula_conductor → conductores.cedula | |
| ausentismo | conductores | ausentismo.cedula → conductores.cedula | |
| incentivos | conductores | incentivos.cedula → conductores.cedula | |
| cierres_diarios | conductores | cierres_diarios.cod_conductor → conductores.codigo | |

## Pitfall: vincular propietario → vehículos
Los códigos de propietario (4 dígitos: 4190, 4288) NO coinciden con los códigos de vehículo en viajes_recaudados (3 dígitos: 500–999). Para encontrar vehículos de un propietario, usar `propietario_nombre` (búsqueda por apellido con `like.*Apellido*`). Ejemplo:

```python
r = requests.get(f"{URL}/rest/v1/viajes_recaudados", headers=h,
    params={"propietario_nombre": "like.*RUEDA*", "select": "placa,codigo_vehiculo"})
```
