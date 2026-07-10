---
name: la-carolina-cerebro-fuentes
description: "Cerebro de fuentes de información de La Carolina (MTC) — bases de datos, APIs, dashboards, equipo, reglas de reporting. Cargar automáticamente cuando Liliana pida análisis de La Carolina."
version: 1.2.0
tags: [la-carolina, datos, supabase, gestivo, dashboards]
required_credential_files:
  - pastes/paste_1_203014.txt
---

# 🧠 Cerebro de Fuentes — La Carolina (MTC)

> ⚡ **Cuando Liliana mencione "La Carolina", "LC", "MTC" o pida cualquier análisis de transporte, carga esta skill inmediatamente.**

✅ **Atajo conversacional:** si Lily dice `tema: cerebro carolina`, `cerebro caroiona`, `cerebro LC` o similar, interprétalo como *modo fuentes de La Carolina*: cargar esta skill, responder con las fuentes disponibles y trabajar desde ellas sin pedir contexto extra.

✅ **Mantenimiento de fuentes:** si Lily comparte un link nuevo y dice `inclúyelo en el cerebro` o `úsalo como fuente`, actualizar esta skill como fuente oficial. Si además pide `súbelo al github del cerebro`, reflejar el cambio en el repo `lanayagiraldo-a11y/cerebro-carolina` Y en las demás copias (ver Entornos).

## 🌍 Entornos — UNA sola versión canónica

Este SKILL.md es **idéntico** en los tres entornos. Al actualizarlo, sincronizar los cuatro destinos:

| Entorno | Ubicación del cerebro | Rutas propias |
|---------|----------------------|----------------|
| **Mac (Claude Code)** — canónico | `~/.claude/skills/la-carolina-cerebro-fuentes/SKILL.md` | Obsidian: `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Boveda Liliana/empresas/la-carolina/` · datos OS: `~/.agentic-os/` |
| **Hermes (servidor Telegram)** | Repo `github.com/lanayagiraldo-a11y/cerebro-carolina` (privado) — Hermes lo consume desde ahí | Obsidian: `/root/obsidian-vault/empresas/la-carolina/` · credenciales: `pastes/paste_1_203014.txt` |
| **Agentic OS (local en esta Mac)** | `~/AI/La Carolina/la-carolina-open-engine/skills/la-carolina/la-carolina-cerebro-fuentes/SKILL.md` | Indicadores: `~/AI/la-carolina-indicadores.json` · specs: `~/AI/agent-os/indicadores/` · datos: `~/.agentic-os/*.json` |
| **Espejo legible (equipo)** | OneDrive `Transporte/MTC/CEREBRO LA CAROLINA/` (docs por sección) | — |

### 🧠 Workflow: buscar fuentes antes de preguntar

Cuando Liliana mencione un archivo, documento o fuente de LC, **NO preguntar "pásamelo" sin antes verificar** en este orden:

1. **Obsidian vault** — `empresas/la-carolina/` (ruta según entorno, ver tabla)
2. **SharePoint** — Reclutamiento → `datastewarlacarolina.sharepoint.com`; Finanzas/Contabilidad → carpeta FINANCIERO-Contabilidad; AdministracionDatos → Control Gerencia y `.raw/sst/`
3. **OneDrive corporativo** — `lanaya@lacarolina.com.co` (token renovado ✅)
4. **Google Drive** — `lanayagiraldo@gmail.com`
5. **Sesiones previas** — `session_search`
6. **Solo si nada funciona** → preguntar a Liliana

⚠️ **Telegram bloquea archivos >20MB.** Si el archivo supera ese límite, ofrecer descargar desde OneDrive o Drive en lugar de pedir que lo reenvíe.

### Reuniones semanales / Flujo de Caja desde Plaud

Cuando Lily pida **"reunión semanal"**, **"flujo de caja"**, **"reunión directiva/directivos de La Carolina"**, o corrija que la reunión **"fue hoy"**, buscar en **Plaud primero** antes de usar notas antiguas de Obsidian. Las fechas/títulos de Plaud pueden reflejar carga/UTC o el día anterior; matchear por tema/título además de fecha. ⚠️ Pitfall: no confundir la reunión semanal de **Flujo de Caja/directivos** con la reunión antigua `2026-06-04` de ahorros/operación/IA.

---

## 🗄️ Bases de Datos

### 1. Supabase #1 — Operacional (principal)
| Campo | Valor |
|-------|-------|
| **URL** | `https://exloqxhnzplovndcjale.supabase.co` |
| **Tipo** | PostgreSQL |
| **Estado** | ✅ Activa |
| **Registros** | ~128,000 en 35 tablas |
| **Datos desde** | 2026-01-01 |

**Tablas principales:**

| Tabla | Registros | Qué contiene |
|-------|----------:|-------------|
| `viajes_recaudados` | 60,822 | Viajes timbrados, ingresos por ruta/turno |
| `cierres_diarios` | 23,503 | Cierres diarios: viajes, timbradas, cumplimiento |
| `ingreso_tercero` | 22,505 | Ingresos de terceros/afiliados |
| `viajes_perdidos` | 17,723 | Viajes perdidos con tipología y novedad |
| `conductores` | 1,192 | Maestro de conductores (todos los registros históricos) |
| `conductores_con_grupo` | — | Conductores con antigüedad + grupo asignado |
| `ausentismo` | 472 | Incapacidades, ausencias con diagnóstico |
| `employees` | 308 | Empleados administrativos |
| `propietarios` | 153 | Propietarios de vehículos afiliados |
| `incentivos` | 82 | Pagos de incentivos a conductores |
| `departments` | 14 | Departamentos |
| `document_categories` | 11 | Categorías documentales |
| `gema_sync_state` | 7 | Estado de sincronización GEMA |
| `pipeline_stages` | 8 | Etapas de reclutamiento |

**Credenciales:** según el entorno — (a) si existen las variables de entorno `$SUPABASE_SERVICE_ROLE_KEY`, `$NEXT_PUBLIC_SUPABASE_ANON_KEY`, `$NEXT_PUBLIC_SUPABASE_URL`, usarlas directo en curl/Bash; (b) en el entorno Hermes están en `~/.hermes/pastes/paste_1_203014.txt` (anon + service role); (c) la anon key readonly también está documentada en `la-carolina-data/references/supabase-rest-api-patterns.md`. ⚠️ Con la anon key aplican restricciones RLS: solo lee `conductores_con_grupo`, `employees` y `gema_sync_state`; las tablas `conductores`, `viajes_perdidos`, `ausentismo`, `cierres_diarios` devuelven vacío — requieren service role key.

---

### 2. Supabase #2 — Conductores
| Campo | Valor |
|-------|-------|
| **URL** | `https://lqeddrpbwunzcyjxuiei.supabase.co` |
| **Tipo** | PostgreSQL |
| **Estado** | ✅ Activa |
| **Contenido** | Solo tabla `conductores` (168 activos, sin placa asignada) + `incentivos` (vacía) |
| **Nota** | Sin datos operacionales. Es un subset de la Supabase #1 |

---

### 3. GESTIVO API — Cerebro unificado
| Campo | Valor |
|-------|-------|
| **Base URL** | `https://saas-six-vert.vercel.app` |
| **Endpoint schema** | `GET /api/external/v1/schema` |
| **Endpoint query** | `POST /api/external/v1/query` |
| **Estado** | ✅ Activa |
| **Tablas** | 19 recursos |

**Recursos disponibles:**

**Rotación:**
- `conductores_con_grupo` — Maestro con antigüedad
- `cierres_diarios` — Cierres: viajes, timbradas, cumplimiento
- `viajes_perdidos` — Viajes perdidos con tipología y novedad

**Accidentalidad:**
- `accidentes` — Reportes de accidentes
- `accidente_evaluaciones` — Evaluaciones de riesgo
- `accidente_vehiculos` — Vehículos involucrados

**RRHH:**
- `incentivos` — Pagos de incentivos
- `candidates` / `vacancies` — Reclutamiento
- `employees` — Maestro de empleados
- `familia` — Dependientes

**Campañas:**
- `meta_campaigns` — Campañas Meta Ads
- `meta_spend_daily` — Gasto diario Meta Ads

**Query example:**
```json
{
  "resource": "conductores_con_grupo",
  "filters": [{ "column": "estado", "op": "eq", "value": "ACTIVO" }],
  "limit": 5
}
```

---

### 4. GEMA MySQL — Histórico 2025
| Campo | Valor |
|-------|-------|
| **Host** | `carolinainstance.c8twjwe0zioh.us-east-2.rds.amazonaws.com` |
| **Puerto** | 3306 |
| **DB** | `gema_cr` |
| **Usuario** | `db_user_2_cr_ext` |
| **Estado** | ❌ Password expirado |
| **Nota** | READONLY. 4 vistas: conductores (1,190), empleados (320), personal (1,648), propietarios (153) + 6 SPs |

---

## 📊 Dashboards Desplegados (Netlify)

| Dashboard | URL | Estado |
|-----------|-----|--------|
| **Dashboard Unificado** | `dashboard-unificado-la-carolina.netlify.app` | ✅ |
| **Reclutamiento** | `la-carolina-reclutamiento-dashboard.netlify.app` | ✅ |
| **Segto. Conductores** | `follow-up-on-driver-rotation-xl2t.vercel.app` | ✅ |
| **Dashboard Conductores** | `la-carolina-dashboard-conductores.netlify.app` | ✅ |
| **Afiliados** | `carolina-afiliados-x7k4q.netlify.app` | ✅ |
| **Agente Financiero** | `agente-financiero-la-carolina.netlify.app` | ✅ |
| **Propuesta Flota** | `propuesta-flota-carolina.netlify.app` | ✅ |
| **Simulador Transmetro** | `simuladortransmetro.netlify.app` | ✅ |
| **Simulador Impacto** | `simulador-impacto-transmetro.netlify.app` | ✅ |
| **Aroca Vives — LC** | `aroca-vives-la-carolina-20260601.netlify.app` | ✅ |

> ⚠️ **Regla:** No desplegar sin orden explícita "deploy". Generar HTML primero y preguntar.

---

## 📁 Otras Fuentes

| Fuente | Acceso | Contenido | Estado |
|--------|--------|-----------|--------|
| **SharePoint — Reclutamiento** | `datastewarlacarolina.sharepoint.com` | `Procesos de reclutamiento.xlsx` (3,654 reg, col MEDIO DE POSTULACION) | ✅ |
| **Google Drive** | `lanayagiraldo@gmail.com` | Carpeta "Transporte - La Carolina" | ✅ |
| **OneDrive Corp** | `lanaya@lacarolina.com.co` | Documentos corporativos ✅ Incluye Brand Manual, Brief Estratégico, logos | ✅ Token renovado |
| **Obsidian Vault** | `empresas/la-carolina/` (ruta según entorno, ver Entornos) | Notas, análisis, reportes por subcarpeta | ✅ |
| **OneDrive — Auditoría Interna (Control Interno)** | `lanaya@lacarolina.com.co` | **Carpeta completa:** Transportes > MTC > Control Interno. 34 archivos con todos los informes de auditoría desde 2019. **Link:** https://datastewarlacarolina-my.sharepoint.com/personal/lanaya_lacarolina_com_co/Documents/Transportes/MTC/Control%20Interno **Resumen:** `informe-detallado-auditoria-interna.md` **Mapa visual:** `mapa-auditorias-control-interno.html` (misma carpeta) | ✅ 34 docs |

**Ruta datos reclutamiento CSV** (Obsidian, según entorno):
`empresas/la-carolina/dashboard-redes-ads-reclutamiento/data/`

**Datos estructurados de flota (placas, internos, marcas):**
repo `lanayagiraldo-a11y/metrocaribe` → `public/data/flota-foton-yutong-placas-internos.md` (en Hermes: `/root/metrocaribe/...`)

### 6. SharePoint FINANCIERO-Contabilidad — Flujo de Caja (agregada 7 jul 2026)
| Campo | Valor |
|-------|-------|
| **URL** | `https://datastewarlacarolina.sharepoint.com/:f:/s/FINANCIERO-Contabilidad/IgAhg3QLv-pBQoOAtWJbp2HWASx2lnpsW3xmPREFe6iJhH4?e=gBoWpK` |
| **Tipo** | Carpeta compartida del sitio SharePoint `FINANCIERO-Contabilidad` (equipo de Edith/Jorge) |
| **Contenido** | Flujo de caja proyectado vs ejecutado (Excel semanal, 52 semanas) |
| **Estado** | ✅ Accesible vía `sharepoint_folder_search`/`read_resource` del conector Microsoft una vez Liliana abrió el link en Chrome (queda indexado). El navegador (Chrome MCP) SÍ pide login — usar el conector de Microsoft en su lugar |
| **Archivo fuente real** | Carpeta `Tesoreria` del sitio → `Proyeccion de Pagos_2026-FEB _V.01 (Comite Caja 2025-10-24).xlsm` (12.2 MB). ⚠️ **`.xlsm` no lo puede leer `read_resource`** (MIME bloqueado) — usar en su lugar el dashboard HTML espejo más reciente que refleje esos datos (buscar `Dashboard_FC_2026.html`, quedarse con el de fecha de modificación más nueva; en jul-2026 el más fresco estaba en `Transporte/INBOX TRANSPORTE/`, semana ejecutada 1–23, NO el de `FINANZAS_GENERAL/Flujo de caja 2026/` que solo llegaba a semana 11) |
| **Datos normalizados** | `~/.agentic-os/la-carolina-flujo-caja.json` (semanas, timbradas real/proy, recaudo real/proy, salidas, saldos) |
| **Dashboard** | Agentic OS → Motor → Indicadores → selector **FC · Flujo de Caja** · API `/api/la-carolina/flujo-caja` |
| **Refrescar** | ✅ **Método actual (8 jul 2026):** correr `python3 ~/.agentic-os/scripts/extract-flujo-caja.py <ruta al .xlsm>` — el script localiza la pestaña semanal de cada semana del año por texto ancla ("Total Recaudo de la Semana" / "Total Pagos de la Semana" / "Saldo Final Caja"), extrae Real vs Proyectado y sobrescribe el JSON con validación (aborta si detecta huecos). El `.xlsm` llega por: (a) repo GitHub `LaCarolinaTPC/Lacarolina_info_mgx_otros` cuando Néstor lo agregue al push diario (pendiente, ver Linear MOT-13), o (b) descarga manual de Liliana a `~/Downloads`. La tarea programada `refrescar-flujo-caja` (7am diario) revisa ambas fuentes sola. ⚠️ Método viejo (HTML espejo `Dashboard_FC_2026.html`): obsoleto — el HTML quedó congelado en marzo 2026, no usarlo |
| **Indicadores derivados** | El mismo JSON alimenta 3 indicadores: **FC** (cumplimiento de recaudo), **PPTO** (cumplimiento de presupuesto de gasto, `/api/la-carolina/presupuesto`) y **DESC** (descalce de caja, `/api/la-carolina/descalce`) — agrupados en Motor → Indicadores → pastilla "Flujo de Caja" |

### 7. GitHub — Repo de datos operativos `LaCarolinaTPC/Lacarolina_info_mgx_otros` (agregada 8 jul 2026)
| Campo | Valor |
|-------|-------|
| **URL** | `https://github.com/LaCarolinaTPC/Lacarolina_info_mgx_otros` (privado — acceso vía `gh` autenticado como `lanayagiraldo-a11y`) |
| **Qué es** | Repositorio de datos operativos que la máquina del **administrador de datos** (`administradordatos@lacarolina.com.co`) sincroniza **sola todos los días a las 9am** (tarea de Windows `LaCarolina-jSONaPI-DailyPush`) — vive en su bóveda Obsidian, carpeta `jSON_aPI/` |
| **Contenido** | CSVs: compras (`Compras_LaCarolina_Rep.csv`), consumos (`Consumo_*.csv`), revisiones de cámara operativa (`LaCarolina_camara.csv`), jurídico-laboral (`Lacarolina_Jur_Lab.csv`), Control Interno ×6 (arqueos caja mayor/menores, venta chatarra, repuestos desmontados, lavados, auditorías — diccionario en `CONTROL-INTERNO.md`), y documentación SGC ISO (`Sgc_Iso/`, 473 archivos md) |
| **⚠️ Cobertura** | SOLO flota propia (La Carolina SAS / Sistemas Prácticos). **La Carolina NO gestiona ni registra los gastos de mantenimiento de la flota AFILIADA** — cada afiliado mantiene su bus por su cuenta; no es un hueco de datos, es la realidad del negocio. No reportar esto como "pendiente" en análisis de afiliados |
| **Cómo leer** | `gh api repos/LaCarolinaTPC/Lacarolina_info_mgx_otros/contents/<archivo> --jq '.content' \| base64 -d` (archivos >1MB: `gh api -H "Accept: application/vnd.github.raw" ...`). Frescura: comparar fecha del último commit (`gh api "repos/.../commits?path=<archivo>&per_page=1"`) |
| **Ventaja clave** | GitHub **no bloquea `.xlsm`** (a diferencia del conector SharePoint) — es la vía para automatizar 100% cualquier archivo de Excel con macros. 📋 Pendiente que **Néstor/administradordatos** agregue el `Proyeccion de Pagos_2026...xlsm` de Tesorería al push diario (Linear MOT-13) para eliminar la descarga manual del flujo de caja |

**Datos ejecutados semana a semana (extraídos de `~/.agentic-os/la-carolina-flujo-caja.json`, corte semana 23 = 9–15 jun 2026):**

| Sem | Rango | Recaudo real | Recaudo proy. | Cumpl. | Salidas real | Salidas proy. | Saldo real | Saldo proy. |
|---:|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | 5 / 11-ene | $544.8M | $584.7M | 93% | $605.4M | $578.0M | $-60.6M | $-37.9M |
| 2 | 12 / 18-ene | $531.6M | $691.3M | 77% | $595.8M | $772.1M | $-109.7M | $-4.5M |
| 3 | 19 / 25-ene | $665.5M | $729.6M | 91% | $613.1M | $1,009.9M | $-57.4M | $-114.5M |
| 4 | 26-ene / 1-feb | $687.5M | $774.9M | 89% | $753.7M | $581.4M | $-120.1M | $-50.0M |
| 5 | 2 / 8-feb | $723.2M | $837.3M | 86% | $825.9M | $635.0M | $-177.7M | $-30.4M |
| 6 | 09 / 15-feb | $708.3M | $844.7M | 84% | $986.9M | $731.0M | $-115.5M | $-4.5M |
| 7 | 16 / 22-feb | $597.8M | $822.3M | 73% | $583.3M | $805.9M | $-100.9M | $-16.1M |
| 8 | 23-feb / 1-mar | $774.2M | $812.1M | 95% | $830.2M | $1,107.0M | $-156.9M | $-44.7M |
| 9 | 2 / 8-mar | $787.4M | $667.9M | 118% | $742.8M | $724.0M | $-97.3M | $-29.3M |
| 10 | 09 / 15-mar | $767.2M | $860.7M | 89% | $753.0M | $772.5M | $-83.1M | $-6.6M |
| 11 | 16 / 22-mar | $764.7M | $841.5M | 91% | $736.4M | $723.9M | $-54.8M | $-9.9M |
| 12 | 23 / 29-mar | $679.6M | $745.8M | 91% | $509.7M | $1,182.4M | $134.0M | $-61.0M |
| 13 | 30 mar / 5-abr | $448.4M | $843.1M | 53% | $689.9M | $765.7M | $-107.5M | $248.6M |
| 14 | 6 / 12-abr | $747.8M | $830.5M | 90% | $463.8M | $503.0M | $187.9M | $-0.9M |
| 15 | 14 / 20-abr | $729.1M | $489.7M | 149% | $754.0M | $751.5M | $163.0M | $-10.4M |
| 16 | 21 / 27-abr | $743.6M | $826.3M | 90% | $887.2M | $1,068.9M | $28.5M | $-38.9M |
| 17 | 28-abr / 4-may | $666.0M | $724.6M | 92% | $597.9M | $869.5M | $96.7M | $344.2M |
| 18 | 5 / 11-may | $755.6M | $818.6M | 92% | $726.2M | $622.0M | $126.0M | $-4.5M |
| 19 | 12 / 18-may | $735.3M | $784.3M | 94% | $791.9M | $924.0M | $85.2M | $-13.8M |
| 20 | 19 / 25-may | $673.6M | $807.2M | 83% | $884.1M | $1,210.2M | $-125.4M | $-60.2M |
| 21 | 26-may / 1-jun | $751.8M | $794.1M | 95% | $552.8M | $810.0M | $73.6M | $332.4M |
| 22 | 2 / 8-jun | $728.6M | $722.7M | 101% | $805.2M | $793.5M | $-2.9M | $-6.7M |
| 23 | 9 / 15-jun | $636.0M | $765.6M | 83% | $815.9M | $646.3M | $-102.8M | $-11.3M |
| **Σ** | **1–23** | **$15,847.7M** | **$17,619.4M** | **89.9%** | **$16,505.1M** | **$18,587.9M** | **$-102.8M** | **$-11.3M** |

Lectura: 15 de las 23 semanas ejecutadas cerraron con saldo de caja negativo (real); el recaudo lleva medio año ~10% por debajo del plan de forma sostenida, no es un mes puntual. Semanas más débiles: #13 (53% cumplimiento) y #7 (73%). Tabla también disponible en vivo (con datos actualizados) en el dashboard: Motor → Indicadores → **FC · Flujo de Caja**.

### 8. SharePoint AdministracionDatos — Carpeta Control Gerencia (agregada 10 jul 2026)
| Campo | Valor |
|-------|-------|
| **Sitio** | `datastewarlacarolina.sharepoint.com/sites/AdministracionDatos` (sitio del administrador de datos) |
| **Link compartido** | https://datastewarlacarolina.sharepoint.com/:f:/s/AdministracionDatos/IgA4vIu5gjBoSJOuhX7hfa2MAXO9zBWpaIlM3RSle1Wp-Zk?e=86X5Y6 |
| **Ruta** | `Documentos compartidos/La Carolina De Transporte/Wiki/Control Interno/CARPETA CONTROL GERENCIA/` |
| **Acceso** | ✅ Conector Microsoft (`sharepoint_search`/`read_resource`) · driveId `b!Sxcixs77jEWxC3oB7LpeeyeUZQ2Oie1JpW8QcgAbc1N7U3SVjNjFRb-SZCp6QA2w` |
| **Contenido** | 6 subcarpetas: 1.Almacén Repuestos-Mtto · 2.Mantenimiento (lavados) · 3.Caja (arqueos) · 4.Cajas Menores · 5.Venta Chatarra · 6.Informes Compartidos — versión viva de los CSVs de Control Interno que llegan al repo GitHub con el push diario 9am |
| **Frescura** | Archivos al día (ARQUEO CAJA MAYOR.xlsx y Revisión Repuestos Desmontados 2026.xlsx modificados 9-jul-2026) |

💡 **Técnica para abrir share links de SharePoint** (`/:f:/s/...` o `/:x:/s/...`) que el conector no abre directo: el token del link codifica el GUID del recurso — base64url decode, bytes 2–17 son el GUID little-endian. Construir el driveItem ID de Graph: `"01" + base32(prefijo_drive_4bytes + GUID_le)`. Prefijo del drive de AdministracionDatos: `13331304`. Luego `read_resource` con `file:///{driveId}/{itemId}`.

### 9. Matriz de Reubicados y Recomendados 2026 — SST (agregada 10 jul 2026)
| Campo | Valor |
|-------|-------|
| **Archivo** | `MATRIZ DE REUBICADOS Y RECOMENDADOS 2026.xlsx` · sitio AdministracionDatos → `La Carolina De Transporte/.raw/sst/` |
| **Link compartido** | https://datastewarlacarolina.sharepoint.com/:x:/s/AdministracionDatos/IQBEuNY3hS95R7yrYeP-IJmYAS7OcFjHbC2aUqp9QTJKcFE?e=g38OlS |
| **Contenido** | Matriz SST de conductores restringidos/reubicados: 17 reubicados laborando + 3 con recomendaciones temporales. Por persona: diagnóstico, EPS/ARL, días de incapacidad, restricciones médicas, cargo actual, junta de calificación, % PCL. ⚠️ **Datos de salud sensibles — tratar con reserva** |
| **Cruce validado 10-jul** | Las 17 cédulas reubicadas = `reubicado='SI'` en `conductores_con_grupo` (100% match). Supabase tiene 19 con flag; 2 de ellos RETIRADO+SI (Guerrero Mendoza, Urueña Mejía) — registrados según la convención correcta (ver abajo) |
| **📐 Convención GEMA para reubicados** (confirmada por Jennifer Pinzón, 10-jul-2026) | GEMA tiene **3 campos separados**: Estado (Activo/Inactivo) · Reubicado (SI/NO) · Retirado (SI/NO). Reubicado que sigue en la empresa = Estado **Inactivo** + Reubicado **SI** + Retirado **NO**, con `fecha_retiro` placeholder futura (2028/2030/2060/2096 — no es error de datos). Cuando sale de la empresa de verdad = se retira de los dos conceptos (Retirado SI + fecha real). Ej. Urueña Mejía: Inactivo + SI + NO + retiro 19/01/2096, observación "FIJO 821, sacaron el vehículo, conductor quedó como relevo de la empresa" |
| **⚠️ Mapeo al sync Supabase** | Supabase NO trae el campo `retirado` de GEMA — colapsa Estado en `estado` (Inactivo→'RETIRADO'). **Heurística para identificar reubicados vigentes en Supabase:** `reubicado='SI'` y (estado ACTIVO, o estado RETIRADO con `fecha_retiro` futura o nula). Si `fecha_retiro` es pasada con estado RETIRADO → ya salió de la empresa. Validado 10-jul: Quezada (retiro 2025-09) y Navarro (2026-01) = se fueron ✓ no están en matriz SST; Guerrero (2030) y Urueña (null/2096) = siguen ✓ están en matriz. El sync es diario → cambios de Jennifer de hoy aparecen mañana |
| **⚠️ Pendiente derivado** | 15 de los 17 reubicados de la matriz siguen como Estado ACTIVO en GEMA — violan la convención e **inflan el conteo de conductores activos hasta en 15**. Verificado contra el sync del 10-jul 2:27pm (dataset `conductores` ok, 1,195 filas): seguían ACTIVO. Jennifer los estaba corrigiendo esa tarde (4:21pm) → re-verificar con el sync del 11-jul. Nota: todos los reubicados tienen `tipo_conductor` = RELEVO TEMPORAL EMPRESA / RELEVO FIJO AFILIADO |
| **📏 REGLA DE CONTEO OFICIAL de conductores activos** | SIEMPRE: `estado = 'ACTIVO'` **Y** `reubicado` distinto de `'SI'`. Nunca contar la base completa ni solo por estado — advertencia textual de Jennifer: "deben estar cogiendo toda la base de datos general como conductor y obviamente va a hacer una mala lectura". Esta regla es robusta antes y después de la migración de los 15 |
| **⏱️ Frescura del sync** | El sync GEMA→Supabase corre a diario (~2:30pm hora Colombia, ver `gema_sync_state.last_run_at`). El `updated_at` de cada fila solo cambia cuando el dato cambió — sirve para detectar cuándo se corrigió un registro |
| **Relación ICO** | ✅ RESUELTO 10-jul-2026: el ICO cuenta reubicados **solo entre los ACTIVO** (auto-adaptativo — cuando GH migre los registros a Inactivo, la resta se apaga sola, sin doble descuento) y expone `reubicadosVigentes` (heurística) para el panel, comparable con la matriz SST (17 al 10-jul). IAC e IRC excluyen reubicados de la población (regla de conteo oficial). Implementado en las 3 APIs del Agentic OS |
| **Nota RLS** | La anon key de Supabase solo lee `conductores_con_grupo` y `employees`; `conductores`, `viajes_perdidos`, `ausentismo`, `cierres_diarios` devuelven vacío (RLS) — requieren service role key |

---

## 👥 Equipo Clave

| Persona | Rol | Área |
|---------|-----|------|
| **John Guerrero** | Gerente Operativo y General | Gerencia |
| **Edith Royero** | Gerente Financiera | Finanzas |
| **Helmuth** | Jefe de Operaciones | Operaciones |
| **Eduardo Luis** | Jefe de Mantenimiento | Mantenimiento |
| **Jennifer Pinzón** | Directora GH | RRHH |
| **Yisseth Dávila** | Psicóloga | RRHH |
| **Cindy Mejía** | Tesorera | Tesorería |
| **Jorge Estrada** | Contador | Contabilidad |
| **Néstor Molina** | Analista de datos | Datos |
| **Víctor Sandoval** | Asesor externo IA | IA |

---

## 🎨 Branding La Carolina (Manual de Marca 2025 + Brief Estratégico Atlántica Marketing)

### Paleta de colores oficial

| Color | Hex | CMYK | RGB | Uso |
|-------|-----|------|-----|-----|
| **Dorado** | `#DCBE61` | 15/22/70/3 | 221/191/97 | Color principal de marca, acentos, KPIs |
| **Rojo** | `#C22219` | 0/95/91/20 | 194/34/25 | Color secundario, botones, llamados a acción |
| **Negro** | `#222222` | 74/64/60/78 | 34/34/34 | Fondos oscuros, textos |
| **Dorado claro** | `#E8D38A` | — | — | Variante dorado, fondos suaves |
| **Rojo oscuro** | `#9A1C14` | — | — | Variante rojo, hover/active |
| **Blanco** | `#FFFFFF` | — | — | Texto sobre fondo oscuro |
| **Gris fondo** | `#F5F5F5` | — | — | Fondos de secciones |
| **Gris borde** | `#E5E5E5` | — | — | Bordes, separadores |

### Tipografía oficial

| Uso | Fuente | Peso |
|-----|--------|------|
| **Títulos** | `Bebas Neue Pro` (o Bebas Neue) | Regular (400) |
| **Cuerpo** | `Montserrat` | Light (300) / Regular (400) |

### Logo
✅ **Logo descargado desde OneDrive** — disponible en:
`assets/logo-la-carolina.png` (PNG, 295 KB)

> Archivo fuente original en OneDrive: `Transporte/MTC/05_Diseños/Diseños/Brand Guidelines/`
> También disponibles: `SelloLaCarolina.pdf`, `Logo-Imagotipo-ELICO` (varios formatos)

### Historia de la marca
- **1990:** Familia agrícola y ganadera llega a Barranquilla. Con crédito de Caja Agraria, compran 15 carros y fundan **"Radio Taxis La Carolina"**
- **Crecimiento:** Llegan a 400 taxis y 100 mecánicos en 5 años
- **1994:** Doña Sofía (madre) enferma, venden flota de taxis y se enfocan en transporte público (microbuses)
- **Expansión:** Rutas Alcón, Ecológica, Carolina Confort → nace **Metropolitana de Transportes La Carolina**
- **Hoy:** Líderes del gremio, pioneros en integración tarifaria

### ADN de Marca

| Dimensión | Descripción |
|-----------|-------------|
| **Arquetipo** | 🧑‍🤝‍🧑 **Amigo (50%)** — Cercano, empático, confiable, "ese amigo que te hace el chance y te da seguridad" |
| | 📚 **Sabio (30%)** — Experto en el sector, educación vial, conocimiento |
| | 🎨 **Creador (20%)** — Innovador, nuevas experiencias, vanguardia |
| **Atributos** | Profesionales comprometidos, Cercano alegre, Confiable, Innovador, Empáticos |
| **Personalidad** | Cálida, auténtica, local, dinámica, energética |

### Taglines oficiales
- **Principal:** "Transporte con corazón"
- **Secundario:** "En Barranquilla el futuro se mueve con La Carolina"
- **Hashtags:** `#TransporteConCorazón`, `#MovemosBarranquilla`, `#LaCarolina`
- **Marca ciudad:** "Somos ejes del progreso de Barranquilla"

### Mensajes clave para comunicación
- "Movemos el progreso de Barranquilla con profesionalismo y seguridad"
- "Seguridad en cada viaje"
- "Tu aliado en el camino hacia el crecimiento"
- "La Carolina mueve el progreso de Barranquilla"
- "Movemos a los que te cuidan"
- "Movemos a los futuros grandes profesionales de la ciudad"

### Propuesta de Valor
> Transporte público seguro, puntual y agradable para usuarios + administración de flotas eficiente y rentable para propietarios e inversionistas.

### Competidores directos
Coochofal, Trasalianso, Alianza Sodis, Coolitoral, Sobusa

### Datos clave del sector
- **700K** personas se movilizan diario en transporte público en Barranquilla
- **Despacho cada 2 min** en hora pico, **cada 6 min** en hora valle
- Una de las empresas con **menor siniestralidad** del sector
- **Pioneros** en integración tarifaria (más de 2 años)

### Palabras clave de la marca
Servicio, Responsabilidad, Profesionalismo, Cercanía, Chévere, Espontáneo, Confiable, Seguridad, Incluyente, Asequible, Local/Barranquilla/Soledad, Orgullo de ciudad

### Look & Feel
- **Si la marca fuera una persona:** Carlos Vives (clásico, cercano, embajador de lo local, auténtico)
- **Personificación:** Carolina — joven, dinámica, alegre, activa, con sensibilidad social

### Reglas de uso en dashboards y reportes
- **Fondo oscuro:** `#222222` para dashboards
- **Fondo claro:** `#F5F5F5` para documentos/formales
- **Títulos:** Bebas Neue Pro, color dorado `#DCBE61`
- **Cuerpo:** Montserrat, blanco `#FFFFFF` sobre fondo oscuro
- **Botones/CTAs:** Rojo `#C22219`
- **Gráficas:** Chart.js con paleta dorado `#DCBE61` + rojo `#C22219` + negro
- **Siempre incluir** logo de La Carolina en header (esquina superior izquierda)
- **Dashboard deploy:** Solo con orden explícita "deploy" — generar HTML primero y preguntar
- **Siempre verificar** números exactos, rango de fechas (desde→hasta), tabla fuente
- **Nunca aproximar** — usar datos verificados

---

## 📋 Proyectos Activos LC

| Proyecto | Estado |
|----------|--------|
| **Yutong** — 4 buses, negociación compensación | 🔴 Deadline |
| **Dashboard Unificado** — Conductores + accidentalidad | 🟢 Activo |
| **App La Carolina** (GitHub + Cursor IDE) | 🟡 En desarrollo |
| **Reporte Afiliados** — Vueltas perdidas | 🟢 Activo |
| **Tablero Ahorros** — Seguimiento quincenal por departamento | 🟢 Activo |
| **Flujo de Caja** — Créditos BBVA, Gases, Serfinanzas | 🔴 Pendiente |
| **Reclutamiento** — Campañas redes + SharePoint | 🟢 Activo |

---

## 📊 Indicadores aprobados (dashboard ejecutivo del Agentic OS)

> **Flujo oficial:** un indicador SOLO entra aquí cuando Liliana dice **"indicador aprobado"**. Al aprobarse: 1) agregarlo a `~/AI/la-carolina-indicadores.json` (registro que lee el tablero ejecutivo `/indicadores` del OS), 2) crear su spec en `~/AI/agent-os/indicadores/<id>.md`, 3) agregar su adapter de resumen en `IndicadoresEjecutivoView.tsx` y en `lib/indicadoresKnowledge.ts` (para que el Hermes flotante responda por él), 4) actualizar esta tabla.

| Indicador | Fórmula | Dónde | Aprobado |
|---|---|---|---|
| **ICO · Cobertura Operativa de Conductores** | (contratados − reubicados − incap. largas >30d) ÷ requeridos, con requeridos = (flota operable × 95%) ÷ tasa asistencia viva · **v1.1 (10-jul-2026):** "reubicados" cuenta solo los que aún figuran ACTIVO (auto-adaptativo a la migración GEMA, sin doble descuento) + panel muestra `reubicadosVigentes` (heurística, comparable con matriz SST) | Vista: Motor → Indicadores · API: `/api/la-carolina/ico` · Spec: `~/AI/agent-os/indicadores/ico-conductores.md` · Doc reubicados: página `/la-carolina/reubicados` del OS (enlazada desde el panel del ICO) | 2026-07-07 |
| **IAC · Accidentalidad de Conductores** | accidentes últimos 30d ÷ conductores activos × 100 · **conductores activos = estado ACTIVO y reubicado ≠ SI** (regla oficial, 10-jul-2026 — el denominador bajó 162→147 y la tasa real subió ~2 pts) · semáforo 🟢≤12 🟡≤20 🔴>20 por 100 conductores/mes · catálogo de severidad: 🟡 incidente (≤1 día afectado) · 🟠 accidente (2-5d) · 🔴 grave (≥6d fuera) · paneles: catálogo, tendencia apilada, aseguradora (app), antigüedad al accidente, watchlist de reincidentes | Vista: Motor → Indicadores → selector IAC · API: `/api/la-carolina/iac` · Spec: `~/AI/agent-os/indicadores/iac-accidentalidad.md` | 2026-07-07 |
| **IRC · Riesgo de Conductores** | Población = conductores `estado ACTIVO` **y** `reubicado ≠ SI` (10-jul-2026: los reubicados no conducen — su seguimiento es de SST, no de riesgo operativo) | Vista: Motor → Indicadores → selector IRC · API: `/api/la-carolina/irc` · Spec: `~/AI/agent-os/indicadores/irc-riesgo-conductores.md` | — |

**Notas de datos aprendidas construyendo el IAC:**
- El histórico de accidentes NO está en las tablas `accidentes` (app, 1 registro de prueba) sino en `viajes_perdidos` con `novedad = ACCIDENTE TRANSITO` (486 vueltas ene–jul 2026). Se estima el número de accidentes agrupando por (cédula, vehículo) con silencio >5 días entre eventos (~205 en el semestre, 113 conductores, ~55 reincidentes).
- Cédula `99999999` = "CONDUCTOR SIN" (no identificado) — excluir de métricas por persona.
- Solo la app `accidentes` captura `solicito_aseguradora`, `hubo_arreglo`/`arreglo_monto` y las evaluaciones (gravedad, reincidencia 3/6/12m, niveles I–IV). 📋 Pendiente que Helmuth registre los accidentes en la app.
- Parámetros manuales del IAC (umbrales, gap clustering, mínimo reincidente): `~/.agentic-os/la-carolina-iac-params.json`, editables desde el dashboard.

**Notas de datos aprendidas construyendo el ICO** (aplican a cualquier consulta):
- ⚠️ **PostgREST corta en 1.000 filas por request** — SIEMPRE paginar con header `Range` en tablas grandes (`viajes_perdidos`, `cierres_diarios`, `candidates`, `viajes_recaudados`), o los totales salen truncados.
- `conductores.reubicado`: campo del maestro GEMA (1/0); el sync lo trae como `SI`/null — aceptar ambos.
- `candidates.created_at` es fecha de importación, NO de postulación — no sirve para ritmo mensual por canal.
- El vínculo candidato↔etapa (`pipeline_stages`) no está en Supabase — conversión del embudo es estimada (10%) hasta que GH registre etapas.
- Parámetros manuales del ICO (buses, vacaciones, embudo): `~/.agentic-os/la-carolina-ico-params.json`, editables desde el dashboard.

---

## 🔗 Links Rápidos

- 🧠 **Cerebro Carolina (GitHub, privado):** `github.com/lanayagiraldo-a11y/cerebro-carolina` — repo privado con este SKILL.md. **No subir a repos públicos.** Si se encuentra una copia en un repo público (ej: `LaCarolinaTPC/la-carolina-skills`), borrarla inmediatamente mediante API de GitHub.
- GitHub nexus-os: `github.com/lanayagiraldo-a11y/nexus-os`
- GitHub ia-masters-os: `github.com/lanayagiraldo-a11y/ia-masters-os`
- GitHub metrocaribe (datos LC): `github.com/lanayagiraldo-a11y/metrocaribe`
- Supabase Studio #1: `supabase.com/dashboard/project/exloqxhnzplovndcjale`
- Supabase Studio #2: `supabase.com/dashboard/project/lqeddrpbwunzcyjxuiei`
- **Dashboard Pendientes Lily:** `dashboard-pendientes-lily.netlify.app`

---

## ⚠️ Notas Importantes

1. **🔒 Seguridad del repo:** El repo `cerebro-carolina` es PRIVADO. Contiene URLs de Supabase, GESTIVO, SharePoint y estructura interna de LC. **No sincronizar ni copiar a ningún repo público.** Si detectas una copia en algún lado, eliminarla inmediatamente.
2. **Dashboard cron 6am:** 🛑 Detenido por orden de Liliana. No reactivar sin nueva orden.
3. **GEMA MySQL:** Password expirado. Preguntar antes de intentar reconectar.
4. **OneDrive corp:** ✅ Token renovado. Brand manual, brief, logos descargados.
5. **GESTIVO API key:** Guardada en sesiones previas de Telegram. Preguntar a Liliana si se pierde.
6. **Telegram file limit:** archivos >20MB no se pueden enviar. Usar OneDrive/Drive como alternativa.
7. Cuando Liliana diga "cron dashboard" o "actualiza dashboard", preguntar primero antes de hacer deploy.
8. **Sincronización del cerebro:** al modificar este SKILL.md, replicarlo en los 4 destinos de la tabla Entornos (Mac canónico → repo GitHub → open-engine/Agentic OS → espejo OneDrive).

---

## 📌 Consultas Frecuentes (queries listas para responder rápido)

Las consultas están organizadas por módulo. Cuando Liliana pregunte algo similar, usar la query correspondiente sin titubear.

### 🚌 Operacional — Viajes

| Pregunta típica | Tabla | Filtro clave | Columnas a mostrar |
|----------------|-------|-------------|-------------------|
| "Viajes perdidos esta semana" | `viajes_perdidos` | fecha >= lunes actual | fecha, ruta, turno, conductor, tipología, detalle |
| "Viajes perdidos este mes" | `viajes_perdidos` | fecha >= 1 del mes | idem + total agrupado por ruta |
| "Viajes perdidos por ruta" | `viajes_perdidos` | fecha (según período) | ruta, COUNT(*), SUM(valor) |
| "Top causas de viajes perdidos" | `viajes_perdidos` | fecha (últimos 90 días) | tipología, COUNT(*), % del total |
| "Total viajes realizados hoy/ayer" | `viajes_recaudados` | fecha específica | COUNT(*) total, por ruta |

### 👷 RRHH — Conductores

| Pregunta típica | Tabla | Filtro clave | Columnas a mostrar |
|----------------|-------|-------------|-------------------|
| "Cuántos conductores activos hay" | `conductores` | estado = ACTIVO | COUNT(*), con/sin placa |
| "Rotación de conductores este mes" | `conductores` | fecha_ingreso y fecha_retiro | ingresados, retirados, saldo neto |
| "Conductores por antigüedad" | `conductores_con_grupo` | activos | grupos 0-3, 3-6, 6-12, +12 meses |
| "Ausentismo esta semana/mes" | `ausentismo` | fecha según período | COUNT(*), días perdidos, diagnóstico |
| "Top 5 diagnósticos de incapacidad" | `ausentismo` | últimos 90 días | diagnóstico, COUNT(*) |
| "Incentivos pagados este mes" | `incentivos` | fecha según período | tipo_incentivo, SUM(valor), conductor |

### 💰 Financiero

| Pregunta típica | Tabla | Filtro clave | Columnas a mostrar |
|----------------|-------|-------------|-------------------|
| "Cierres diarios de ayer/hoy" | `cierres_diarios` | fecha específica | viajes, timbradas, cumplimiento, déficit |
| "Déficit acumulado del mes" | `cierres_diarios` | fecha >= 1 del mes | SUM(ingresos), SUM(gastos), saldo |
| "Comparativa ingresos vs mes pasado" | `cierres_diarios` | mes actual vs mes anterior | ingresos, egresos, variación % |
| "Ingresos de terceros este mes" | `ingreso_tercero` | fecha según período | SUM(valor), agrupado por concepto |

### 📢 Campañas y Reclutamiento

| Pregunta típica | Tabla/Fuente | Filtro clave | Columnas a mostrar |
|----------------|-------------|-------------|-------------------|
| "Gasto en Meta Ads este mes" | `meta_spend_daily` | fecha según período | SUM(spend), por campaña |
| "Candidatos por medio de postulación" | SharePoint Excel | — | medio_postulacion, COUNT(*) |
| "Vacantes abiertas" | `vacancies` | estado = ABIERTA | título, fecha, área |

### ⚠️ Anomalías (transversal)

| Pregunta típica | Qué revisar | Cómo detectarlo |
|----------------|-------------|-----------------|
| "Algo raro en la operación hoy?" | viajes_perdidos vs promedio 7 días | Si viajes_perdidos hoy > 2x promedio → alerta 🔴 |
| "Pico de ausentismo" | ausentismo vs promedio mensual | Si > 30% del promedio → alerta 🟡 |
| "Déficit inusual" | cierres_diarios vs promedio semanal | Si déficit > 1.5x promedio → alerta 🟠 |

### 🎨 Formato de respuesta
- **Siempre** incluir: rango de fechas (desde → hasta), tabla fuente, branding LC
- Si son 3+ filas: tabla Markdown
- Si son 10+ filas: tabla + sugerir dashboard
- Si es comparativa: incluir variación % y emoji 📈📉
- Colores: usar dorado `#DCBE61` para totales, rojo `#C22219` para alertas

### 📊 Prioridad de fuente por tipo de consulta

| Tipo de consulta | Fuente primaria | Fuente secundaria |
|-----------------|----------------|-------------------|
| Operación diaria (viajes) | **Supabase #1** | GESTIVO (si falla) |
| Conductores y RRHH | **Supabase #1** + GESTIVO | — |
| Dashboard histórico | **GESTIVO** | Supabase #1 |
| Campañas Meta Ads | **GESTIVO** | — |
| Reclutamiento | **SharePoint** | GESTIVO |
| Alertas/anomalías | **Supabase #1** + cálculos locales | — |
