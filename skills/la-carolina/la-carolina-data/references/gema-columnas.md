# GEMA MySQL — Esquema de columnas

## vst_ext_get_conductores (~1,190 registros)

| Columna | Tipo | Nota |
|---|---|---|
| `codigo_personal` | varchar(5) | Código interno |
| `estado` | tinyint(1) | 1=activo |
| `retirado` | int | 0=no retirado |
| `num_licencia` | varchar(50) | Número de licencia |
| `vencimiento_licencia` | date | Fecha vencimiento licencia |
| `ahorro` | decimal(10,0) | Ahorros |
| `vencimiento_contrato` | date | Vencimiento contrato |
| `bloqueo` | int | Bloqueo |
| `reubicado` | int | Reubicado |
| `fecha_ingreso` | date | Ingreso a la empresa |
| `fecha_retiro` | date | Retiro |
| `fecha_inicio_exp` | date | Inicio experiencia |
| `observacion` | varchar(250) | Observaciones |
| `tipo_sangre` | varchar(4) | RH |
| `nombre` | varchar(204) | Nombre completo |
| `identificacion` | bigint | Número de cédula |
| `tipo_identificacion` | varchar(255) | CC, NIT, etc. |
| `fecha_nacimiento` | date | Fecha de nacimiento |
| `tipo_coductor` | varchar(255) | Tipo de conductor (nota: typo original "coductor") |
| `direccion` | varchar(155) | Dirección |
| `telefono` | varchar(100) | Teléfono fijo |
| `celular` | varchar(100) | Celular |
| `arl` | varchar(255) | ARL |
| `pension` | varchar(255) | Fondo de pensiones |
| `compensacion` | varchar(255) | Caja de compensación |
| `eps` | varchar(255) | EPS |
| `nivel_educativo` | varchar(255) | Nivel educativo |
| `num_hijos` | int | Número de hijos |
| `estado_civil` | varchar(255) | Estado civil |

## vst_ext_get_empleados (~320 registros)

| Columna | Tipo | Nota |
|---|---|---|
| `codigo_personal` | varchar(5) | Código interno |
| `nombre` | varchar(204) | Nombre completo |
| `estado` | tinyint(1) | 1=activo |
| `retirado` | int | 0=no retirado |
| `incapacitado` | int | 1=incapacitado |
| `vencimientoContrato` | date | Vencimiento contrato |
| `fecha_ingreso` | date | Ingreso |
| `fecha_retiro` | date | Retiro |
| `fecha_inicio_exp` | date | Inicio experiencia |
| `tipo_sangre` | varchar(4) | RH |
| `identificacion` | bigint | Cédula |
| `direccion` | varchar(155) | Dirección |
| `celular` | varchar(100) | Celular |
| `fnacimiento` | date | Fecha nacimiento |
| `cargo` | varchar(255) | Cargo |
| `grupo` | varchar(255) | Grupo |
| `departamento` | varchar(255) | Departamento |
| `tipo_identificacion` | varchar(255) | CC, NIT, etc. |
| `arl` | varchar(255) | ARL |
| `pension` | varchar(255) | Pensión |
| `compensa` | varchar(255) | Compensación |
| `eps` | varchar(255) | EPS |
| `nivel_educativo` | varchar(255) | Nivel educativo |
| `num_hijos` | int | Hijos |
| `estado_civil` | varchar(255) | Estado civil |
| `grupo_interno` | varchar(100) | Grupo interno |

## vst_ext_get_personal (~1,648 registros — unifica todo)

| Columna | Tipo | Nota |
|---|---|---|
| `codigo` | varchar(5) | Código interno |
| `identificacion` | bigint | Cédula |
| `pnombre` | varchar(50) | Primer nombre |
| `snombre` | varchar(50) | Segundo nombre |
| `papellido` | varchar(50) | Primer apellido |
| `sapellido` | varchar(50) | Segundo apellido |
| `nombre_completo` | varchar(203) | Nombre completo |
| `direccion` | varchar(155) | Dirección |
| `telefono` | varchar(100) | Teléfono |
| `celular` | varchar(100) | Celular |
| `correopersonal` | varchar(100) | Correo personal |
| `sexo` | enum('M','F') | Sexo |
| `fnacimiento` | date | Fecha nacimiento |
| `numero_cuenta` | varchar(20) | Número de cuenta bancaria |
| `num_hijos` | int | Hijos |
| `codigo_ciudad` | int | Código ciudad |
| `ciudad` | varchar(100) | Ciudad |
| `codigo_departamento` | int | Código departamento |
| `departamento` | varchar(100) | Departamento |
| `is_empleado` | varchar(2) | Flag empleado (SI/NO) |
| `is_propietario` | varchar(2) | Flag propietario (SI/NO) |
| `is_conductor` | varchar(2) | Flag conductor (SI/NO) |
| `is_usuario` | varchar(2) | Flag usuario (SI/NO) |

## vst_ext_get_propietarios (~153 registros)

| Columna | Tipo | Nota |
|---|---|---|
| `codigo_personal` | varchar(5) | Código interno |
| `estado` | tinyint(1) | 1=activo |
| `nombre` | varchar(204) | Nombre completo |
| `identificacion` | bigint | Cédula/NIT |
| `tipo_identificacion` | varchar(255) | CC, NIT, etc. |
| `tipo_propietario` | varchar(255) | Tipo de propietario |
| `plazo_pago` | varchar(255) | Plazo de pago |
| `direccion` | varchar(155) | Dirección |
| `telefono` | varchar(100) | Teléfono |
| `celular` | varchar(100) | Celular |
| `correopersonal` | varchar(100) | Correo personal |
