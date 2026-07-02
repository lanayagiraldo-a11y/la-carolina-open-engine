---
name: reporte-bus-carolina
description: Genera un reporte de 1 página (PDF con branding La Carolina) con TODO lo que se sabe de una buseta o un conductor de Metropolitana de Transportes La Carolina. Úsalo cuando Liliana diga el número de un bus ("reporte del bus 622", "qué sabes de la 622", "analiza la buseta 977") o el nombre/cédula de un conductor ("info del conductor Hurtado", "reporte de la conductora con cédula 72282385"). Cubre afiliado/propietario, modelo, placa, días operados, vueltas perdidas por causa, días en taller, ausencias del conductor con su motivo, patrones por mes y día. Período 1 ene–31 may 2026.
---

# Reporte de Buseta o Conductor — La Carolina

Genera un reporte ejecutivo de **1 página**, con el **branding oficial de La Carolina** (dorado #DCBE61 / rojo #C22219 / negro #222222, Bebas Neue + Montserrat, slogan "Transporte con corazón"), de todo lo conocido sobre **una buseta** o **un conductor**.

## Cuándo usar
Cuando Liliana:
- Da un **número de bus**: "reporte del bus 622", "qué sabes de la 977", "analízame la buseta 841".
- Da un **conductor** (nombre o cédula): "info del conductor Hurtado Quiroga", "reporte del conductor con cédula 72282385".
- Pide un **reporte para afiliados**, **vueltas perdidas**, **novedades del chat de afiliados**, o un ranking por **propietario/bus**. Para este flujo de reporte agregado, usa `references/afiliados-vueltas-perdidas.md` antes de generar archivos.

## Cómo ejecutar

```bash
python3 ~/.hermes/skills/la-carolina/reporte-bus-carolina/generar_reporte.py bus <NUMERO>
python3 ~/.hermes/skills/la-carolina/reporte-bus-carolina/generar_reporte.py conductor "<NOMBRE O CEDULA>"
```

Ejemplos:
```bash
python3 ~/.hermes/skills/la-carolina/reporte-bus-carolina/generar_reporte.py bus 622
python3 ~/.hermes/skills/la-carolina/reporte-bus-carolina/generar_reporte.py conductor "HURTADO QUIROGA"
python3 ~/.hermes/skills/la-carolina/reporte-bus-carolina/generar_reporte.py conductor 72282385
```

El script imprime un RESUMEN (para que se lo cuentes a Liliana en el chat) y la ruta del PDF generado.
El PDF y el HTML quedan en: `OneDrive/Transporte/MTC/AFILIADOS 2026/`.

## Pasos del agente
1. Identifica si Liliana pide un **bus** (número) o un **conductor** (nombre/cédula).
2. Ejecuta el script con el modo correcto.
3. Lee el RESUMEN que imprime el script y cuéntaselo a Liliana en lenguaje natural (con tablas si aplica).
4. Verifica el PDF y entrégalo por Telegram con `MEDIA:<ruta_del_pdf>` si Liliana lo quiere descargar. En Linux no uses `open`; usa `pdfinfo`/`python` para validar y entrega el archivo.
5. Ofrece copiarlo a la bóveda si lo quiere archivar: `/root/obsidian-vault/empresas/la-carolina/Operacion y flota/afiliados-2026-archivos/`.

## Qué contiene el reporte
**Buseta:** afiliado/propietario, placa, modelo y edad, ruta, días operados / sin operar, vueltas perdidas por causa (taller, ausencia conductor, varado, etc.), % por vehículo, días en taller, **ausencias del conductor con su motivo** (permiso, incapacidad, etc.), patrones por mes y por día, y un resumen-diagnóstico.

**Conductor:** nombre y cédula, días con operación, buses y rutas que manejó, vueltas perdidas asociadas, ausencias con su motivo, y resumen.

## Reglas importantes
- **Período:** 1 enero – 31 mayo 2026. Siempre indica las fechas reales de los datos del bus/conductor (el script las calcula).
- **Solo "ausencia del conductor"** = el conductor existía y faltó. NO se reporta como problema la "falta de conductor para el bus" / "sin conductor fijo" salvo que Liliana lo pida.
- **Branding La Carolina** obligatorio (ver `empresas/la-carolina/.../manual-de-marca-la-carolina.md` en la bóveda). Nunca usar azul/verde/naranja.
- El reporte SIEMPRE debe caber en **1 página**.

## Fuentes de datos

### Opción A: Supabase (preferida — siempre disponible desde Hermes)
El proyecto Supabase `exloqxhnzplovndcjale` contiene 35 tablas con 128,004 registros operativos de La Carolina. Para consultar:

```python
import re, requests
# Leer credenciales desde ~/.hermes/pastes/paste_1_203014.txt
# o desde variables de entorno
KEY = os.environ.get('SUPABASE_SERVICE_KEY')
URL = "https://exloqxhnzplovndcjale.supabase.co"
h = {"apikey": KEY, "Authorization": f"Bearer {KEY}"}
r = requests.get(f"{URL}/rest/v1/{tabla}", headers=h, params=params)
```

**Vinculación propietario → vehículos:** NO usar `codigo_propietario`. La columna correcta es `propietario_nombre` en `viajes_recaudados` (buscar con `like.*apellido*`). La tabla `propietarios` tiene cédula, código y nombre. Los códigos de vehículo en `viajes_recaudados` (3 dígitos: 504, 541, etc.) NO corresponden a los códigos de propietario (4 dígitos: 4190, 4288). Usar siempre nombre o cédula del propietario para vincular.

**Paginación:** Supabase REST API limita a 1,000 registros por request. Usar paginación con `limit` y `offset`:

```python
def get_all(tabla, **params):
    data, offset = [], 0
    while True:
        r = requests.get(f"{URL}/rest/v1/{tabla}", headers=h,
            params={**params, "limit": 1000, "offset": offset})
        batch = r.json()
        if not batch: break
        data.extend(batch); offset += len(batch)
    return data
```

**Tablas clave y sus columnas de enlace:**
| Tabla | Columnas clave | Enlace |
|---|---|---|
| `viajes_recaudados` | `codigo_vehiculo`, `placa`, `propietario_nombre`, `cedula_propietario`, `conductor_nombre`, `cedula_conductor` | Centro de viajes |
| `viajes_perdidos` | `placa`, `vehiculo`, `novedad`, `fecha`, `ruta` | Por placa |
| `conductores` | `cedula`, `nombre`, `estado` | Por cédula |
| `propietarios` | `cedula`, `codigo`, `nombre` | Por cédula |
| `ausentismo` | `cedula`, `dias_it_pagados`, `diagnostico` | Por cédula |
| `incentivos` | `cedula`, `valor`, `concepto` | Por cédula |
| `cierres_diarios` | `fecha`, `ruta`, `timbradas`, `viajes` | Datos financieros |

### Opción B: Archivos locales (original — requiere Mac y OneDrive sincronizado)
- Preferencia 1: `$LC_MTC_DIR/00_INBOX MTC/Viajes perdidos.xlsx` — novedades por bus/conductor con causa, fecha, vuelta.
- Preferencia 1: `$LC_MTC_DIR/00_INBOX MTC/VEHICULO - CONDUCTORES  A MAYO 2026.xlsx` — conductor–bus–propietario–días–ruta.
- Alternativa Google Drive consolidada: `$LC_MTC_DIR/Consolidados_Operativo/Operativo_Vjperdidos_consolidado.csv` — viajes perdidos separados por `;`.
- Alternativa Google Drive consolidada: `$LC_MTC_DIR/Consolidados_Operativo/Base_comision_Conductores_consolidado.csv` — liquidación vehículo/conductor separada por `;`.
- `data/flota_modelo.csv` (dentro del skill) — código → placa → modelo.

Si algún archivo cambió de nombre o ubicación, define `LC_MTC_DIR` al directorio `Transporte/MTC` o `LC_VIAJES_XLSX` / `LC_VEHCOND_XLSX` con rutas exactas. La salida puede redirigirse con `LC_AFILIADOS_REPORT_OUT`. En Linux/Hermes como root, el script ya usa Chromium headless con `--no-sandbox` y valida que el PDF exista.
