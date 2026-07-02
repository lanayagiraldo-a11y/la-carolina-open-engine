---
name: afiliados-mtc
description: >
  Genera reportes individuales de buses afiliados de La Carolina (MTC) para la reunión de afiliados.
  Úsala cuando Liliana diga "reporte del bus X", "analiza el afiliado X", "qué tiene el bus X",
  "genera la lámina del bus X", "reporte del propietario X", o cualquier variación referida a un
  bus o propietario específico de la flota afiliada. También úsala si dice "genera todos los
  reportes" o "batch de afiliados". El reporte incluye: ficha del vehículo, vueltas perdidas por
  causa, desglose por mes/día/vuelta del día/ruta, conductor fijo, conductores por mes, ingreso
  bruto no generado por fallas del vehículo usando timbradas reales diferenciadas L-V vs S-D
  (timbradas × $3.300). Branding oficial La Carolina: dorado #DCBE61, rojo #C22219, negro #222222.
  Salida: HTML + PDF de 1 página en AFILIADOS 2026/Reportes por buseta y conductor/.
---

# Skill: Reportes Afiliados MTC

## Contexto

Estás trabajando con los datos operativos de **Metropolitana de Transportes La Carolina (MTC)**,
Barranquilla. La flota afiliada tiene 88 buses y 40 propietarios. El período de análisis es
**1 enero – 31 mayo 2026 (151 días)**.

El reporte de cada bus muestra cuánto dejó de producir por fallas del vehículo, con el objetivo
de motivar al afiliado a hacer **mantenimiento preventivo** — no para señalar la edad del bus ni
para pedir reemplazo.

---

## Paso 1 — Identificar el bus (o buses)

| Lo que dice | Lo que necesitas |
|---|---|
| "reporte del bus 622" | número de bus: `622` |
| "lámina de la 622" | número de bus: `622` |
| "qué tiene el bus de González Galvis" | buscar propietario en los datos |
| "genera todos los reportes" | modo batch: todos los buses afiliados |

Si el número de bus no es claro, pregunta antes de ejecutar.

---

## Paso 2 — Ejecutar el generador

```bash
cd ~/.hermes/skills/la-carolina/reporte-bus-carolina
python3 generar_reporte.py bus <NÚMERO>
```

### Reporte de un conductor:

```bash
python3 generar_reporte.py conductor "APELLIDO NOMBRE"
# o por cédula:
python3 generar_reporte.py conductor 72282385
```

### Batch — todos los buses afiliados:

Confirma con Liliana antes (puede tardar varios minutos), luego:

```bash
python3 - <<'EOF'
import csv
with open('/root/.hermes/skills/la-carolina/reporte-bus-carolina/data/flota_modelo.csv') as f:
    buses = [row[0] for row in csv.reader(f) if row]
print('\n'.join(buses))
EOF
```

```bash
for bus in <lista>; do
    python3 generar_reporte.py bus "$bus"
done
```

---

## Paso 3 — Verificar el PDF

```bash
pdfinfo "<ruta_al_pdf>" | grep Pages
```

Si dice más de 1 página, avisa a Liliana.

---

## Paso 4 — Mostrar el resultado

```bash
# En Telegram: entregar con MEDIA:<ruta_al_pdf>
```

Resumen en el chat:

```
✅ REPORTE BUS <N> generado
Propietario: <nombre>
Modelo: <año> · Placa: <placa>
Vueltas perdidas: <total> (vehículo: <veh_perd>, conductor: <aus_perd>)
Timbradas no registradas: <timbradas_perdidas>  (L-V: <tim_sem>/vuelta · S-D: <tim_fds>/vuelta)
Ingreso bruto no generado: $<ingreso_perdido> COP
Conductor fijo: <nombre> (<días> días)
PDF: <ruta>
```

---

## Qué calcula el script (lógica actual)

| Métrica | Fuente | Cómo |
|---|---|---|
| Vueltas perdidas | `Viajes perdidos.xlsx` hoja "Base D" | Contar filas del bus, col 7 = Vehiculo |
| Causa de pérdida | Col 2 = Novedad | TALLER + VARADO EN RUTA = falla vehículo |
| Desglose por mes | Col 4 = Fecha | Agrupa por mes; filas sin fecha → "Sin fecha" |
| Desglose por día | Col 4 = Fecha | weekday() 0=Lun … 6=Dom; 7="Sin fecha" |
| Desglose por vuelta del día | Col 12 = Viaje (ordinal 1-5) | Cuenta por ordinal; sin dato → 0 |
| Desglose por ruta | Col 13 = Ruta | Normaliza guiones dobles antes de contar |
| Conductor fijo | `VEHICULO-CONDUCTORES.xlsx` col 8 | El que más días únicos (set de fechas) |
| Conductores por mes | Mismo archivo, col 8 + fecha[:7] | Set de nombres por mes |
| Timbradas reales L-V | Mismo archivo, cols 13=viajes, 15=timbradas | Solo filas weekday < 5, tim > 5 |
| Timbradas reales S-D | Mismo archivo, cols 13=viajes, 15=timbradas | Solo filas weekday >= 5, tim > 5 |
| Timbradas perdidas | Por falla vehículo × rate del día | Suma vuelta a vuelta con rate correcto |
| Ingreso bruto no generado | timbradas_perdidas × $3.300 | Métrica del bus, NO del conductor |

**Todos los subtotales (mes + día + vuelta + ruta) suman igual al total de vueltas perdidas.**

---

## Datos clave de la flota (no recalcules)

| Métrica | Valor |
|---|---|
| Total vueltas perdidas afiliadas | 9.141 |
| % por vehículo | 54% (taller 42% + varado 12%) |
| Promedio vueltas/vehículo perdidas | 57 |
| Tarifa por timbrada | $3.300 COP |
| Período | 151 días (1 ene – 31 may 2026) |

---

## Fuentes de datos

| Archivo | Uso |
|---|---|
| `00_INBOX MTC/Viajes perdidos.xlsx` hoja "Base D" | Vueltas perdidas y causas |
| `00_INBOX MTC/VEHICULO - CONDUCTORES A MAYO 2026.xlsx` | Liquidación diaria: viajes, timbradas reales, conductor, propietario |
| `~/.hermes/skills/la-carolina/reporte-bus-carolina/data/flota_modelo.csv` | Modelo y placa por bus |

Salida siempre en:
`OneDrive/Transporte/MTC/AFILIADOS 2026/Reportes por buseta y conductor/`

### Link público / tablero afiliados

Cuando Liliana pida “el link de afiliados”, el enlace público operativo registrado es:

- `https://carolina-afiliados-x7k4q.netlify.app/`
- Título visible: “¿Y tu bus? · La Carolina · Junta de Afiliados 2026”.

Antes de responder en una sesión futura, si hay tiempo, verificar que el link sigue resolviendo. Si Liliana pide desplegar o actualizar el reporte, recordar que contiene información operativa sensible de afiliados; no publicar variantes nuevas sin confirmar alcance.

---

## Tono para la reunión de afiliados

- ✅ "Mantenimiento oportuno = más vueltas completadas = más timbradas para todos"
- ✅ "Ingreso bruto no generado por fallas del vehículo"
- ❌ No mencionar que el conductor "no cobró" (riesgo legal)
- ❌ No señalar la edad del bus como el problema central
- ❌ No pedir reemplazo del bus

---

## Bonus: Presentación general para reunión de afiliados

Además de los reportes individuales, Liliana puede pedir una **presentación completa** para la
reunión de afiliados. Usar este flujo.

> ⚠️ **OJO CON LAS IMÁGENES**: Si Liliana envía imágenes con gráficas/datos, NO asumas que son solo referencia visual. Extraer los datos con `tesseract "$img" stdout -l spa` e incorporarlos como texto y KPIs en el HTML. Las imágenes se copian como assets a `/root/presentacion-assets/`.

### 1. Extraer datos fuente
- Extraer texto de actas con `pdftotext -layout`
- Buscar en bóveda Obsidian: `empresas/metrocaribe-transmetro/` — datos de
  factor-calidad-fq.md, flujo-caja-finanzas-metrocaribe.md, transporte-masivo.md
- Identificar datos duros: -33% movilidad, $787M/día, $1.200M déficit
- **OCR a imágenes:** `tesseract img.jpg stdout -l spa` para extraer datos de gráficas

### 2. Obtener branding real
- Cargar skill `la-carolina-designer` con `skill_view(name='la-carolina-designer')`
- Usar el logo real desde `assets/logos/logo-principal-transparente.png`
- Redimensionar a 200×200 con PIL, convertir a base64 para incrustar en HTML
- Siempre incluir slogan "Transporte con corazón"

### 3. Extraer action items de actas PDF

Cuando Liliana envía PDFs de transcripciones de reuniones (de Plaud):

```bash
pdftotext -layout "archivo.pdf" -
```

Buscar en el texto:
- Compromisos/acciones específicas con responsable asignado
- Fechas de entrega ("lunes 6 de julio", "semana del 7")
- Números y datos duros mencionados por los speakers
- Decisiones sobre combustible, despacho, programación

Cuando los afiliados mencionan puntos específicos del acta, agruparlos como **compromisos internos** (lo que la empresa hace por ellos) vs **acciones gremiales** (lo que el gremio exige a autoridades).

### 4. Arquitectura de slides (9 slides, 3 partes)

**Parte I · El Panorama (slides 2-5)**
1. **Portada** — Logo real + "Transporte con corazón" + título
2. **Panorama General TPC** — KPIs: -33,1% TPC, 767K→513K pasajeros, SITM 135K→62K (-54%), motos 13.360→29K (+117%), +4.000 motocarros ilegales. Efecto sustitución. Cita: "No es que la gente prefiera la moto — es imposible competir en las condiciones actuales".
3. **Transmetro: Más flota, menos pasajeros** — +11,1% flota / -10,6% pasajeros. 130 vehículos promedio. TPC y Transmetro caen al mismo ritmo (-10,3% y -10,6%). SITM perdió -54% pasajeros 2019-2025. Alerta: más buses no recuperan la demanda sin control de informalidad.
4. **Costos y Finanzas** — Déficit $1.200M, deuda SS $115M, FQ $56.75B, inflación agrava brecha, cada pasajero genera pérdida operativa
5. **Escasez Conductores** — Crisis nacional, SENA, 28+ carros parados/día. Cita: "El transporte público no va a tener operadores"

**Parte II · Acciones Gremiales (slides 6-7)**
6. **Plan de Acción del Gremio** — 5 acciones: ① Operativos contra ilegalidad (lun 6 jul) ② Plan desvíos cierres viales ③ Participación PND (cambiar "podrán" por "tendrán") ④ Nulidad Art. 5 Circular 001/2026 ⑤ Exigencia a Transmetro
7. **Responsabilidad del AMB** — Resolución 197, acuerdos metropolitanos, obligación de sostenibilidad. Advertencia: si no se atienden necesidades → incumplimiento

**Parte III · Compromisos con Afiliados (slide 8)**
8. **6 Compromisos:**
   ① Situación transporte — medidas en curso
   ② Programación — cambios enturnamiento (Helmut, 50 carros Miramar)
   ③ Combustible — Andrés Anaya presenta (solo título si él expone)
   ④ Despacho — alivio transitorio julio, % pendiente, Sintramas se suma
   ⑤ Beneficios año — desmonte gradual, corte a 6 meses
   ⑥ Buses viejos — retiro progresivo, reposición condicionada a tasas

9. **Cierre** — Próximos pasos con fechas. Cita de cierre: "No podemos cumplir si no se garantizan condiciones mínimas"

### 4. Branding
- Logo real como base64 (redimensionar a 200×200 con PIL LANCZOS, ~14KB)
- Colores: `#DCBE61` dorado, `#C22219` rojo, `#222222` negro
- Fuentes: Bebas Neue (títulos) + Montserrat (cuerpo)
- Slogan "Transporte con corazón" en rojo sobre portada
- KPIs en cards con números grandes, action items numerados, alert boxes para advertencias

### 5. Pitfalls
- **No asumir orden correcto la primera vez** — Liliana reorganiza visualmente después de verlo
- **Si falta un % exacto**, dejarlo como "pendiente de definir" o "Andrés Anaya presenta"
- **Si dice "solo pon el título"**, respetarlo — esa persona presentará ese tema
- **Al eliminar slides**, actualizar TODOS los números y etiquetas de sección
- **Verificar numeración** con `grep -n 'slide-number\|SLIDE [0-9]:' archivo.html | grep -v '\.slide-number'` — buscar duplicados
- **Después de múltiples patches de reordenamiento**, pueden quedar DIVs sueltos o footers huérfanos. Leer la zona alrededor de cada slide modificado para detectarlos.
- **Al reordenar slides**, actualizar: comentario SLIDE N, slide-number, section-sub (Parte I/II/III), y referencias cruzadas
- **Si Liliana envía imágenes**, extraer los datos con OCR **antes** de construir el HTML — incorporar como texto, no solo como imágenes de referencia
- **El logo real** debe ir como base64 inline (redimensionado a ~200px). No usar texto "LA CAROLINA" como sustituto del logo a menos que no haya acceso al PNG.
- **Siempre dejar el HTML editable** para que Liliana pueda ajustar texto antes del PDF

---

## Si algo falla

| Problema | Solución |
|---|---|
| "No encontré datos del bus X" | Verificar que el bus es afiliado; puede no tener novedades en el período |
| PDF de más de 1 página | Hay contenido muy largo — revisar campos de propietario o conductor |
| Error de openpyxl | `pip3 install openpyxl` |
| OneDrive no disponible | Los xlsx necesitan estar descargados localmente (no en la nube) |
| Subtotales no cuadran | El script ya maneja filas sin fecha/vuelta/ruta con categoría "Sin dato" |


---

## Adaptación Hermes instalada

- Script instalado en: `~/.hermes/skills/la-carolina/reporte-bus-carolina/generar_reporte.py`.
- En este servidor Linux, las rutas de datos se resuelven así:
  - `LC_MTC_DIR` → carpeta base `Transporte/MTC` que contiene `00_INBOX MTC/`.
  - `LC_VIAJES_XLSX` → ruta exacta alternativa para `Viajes perdidos.xlsx`.
  - `LC_VEHCOND_XLSX` → ruta exacta alternativa para `VEHICULO - CONDUCTORES  A MAYO 2026.xlsx`.
  - `LC_AFILIADOS_REPORT_OUT` → carpeta de salida alternativa.
- El PDF se genera con `chromium-browser`/`google-chrome` en modo headless y se entrega a Liliana por Telegram como `MEDIA:<ruta_pdf>`.
- Si los Excel no están descargados/locales, primero hay que traerlos desde OneDrive/Graph o pedirlos como adjunto.
