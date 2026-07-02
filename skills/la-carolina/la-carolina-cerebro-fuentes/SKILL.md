---
name: la-carolina-cerebro-fuentes
description: "Cerebro de fuentes de información de La Carolina (MTC) — bases de datos, APIs, dashboards, equipo, reglas de reporting. Cargar automáticamente cuando Liliana pida análisis de La Carolina."
version: 1.0.0
tags: [la-carolina, datos, supabase, gestivo, dashboards]
---

# 🧠 Cerebro de Fuentes — La Carolina (MTC)

> ⚡ **Cuando Liliana mencione "La Carolina", "LC", "MTC" o pida cualquier análisis de transporte, carga esta skill inmediatamente.**

### 🧠 Workflow: buscar fuentes antes de preguntar

Cuando Liliana mencione un archivo, documento o fuente de LC, **NO preguntar "pásamelo" sin antes verificar** en este orden:

1. **Obsidian vault** — `/root/obsidian-vault/empresas/la-carolina/`
2. **OneDrive corporativo** — `lanaya@lacarolina.com.co` (token renovado ✅)
3. **Google Drive** — `lanayagiraldo@gmail.com`
4. **Sesiones previas** — `session_search`
5. **Solo si nada funciona** → preguntar a Liliana

⚠️ **Telegram bloquea archivos >20MB.** Si el archivo supera ese límite, ofrecer descargar desde OneDrive o Drive en lugar de pedir que lo reenvíe.

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

**Credenciales:** Guardadas en `~/.hermes/pastes/paste_1_203014.txt` (anon key + service role key).

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
| **SharePoint** | `datastewarlacarolina.sharepoint.com` | `Procesos de reclutamiento.xlsx` (3,654 reg, col MEDIO DE POSTULACION) | ✅ |
| **Google Drive** | `lanayagiraldo@gmail.com` | Carpeta "Transporte - La Carolina" | ✅ |
| **OneDrive Corp** | `lanaya@lacarolina.com.co` | Documentos corporativos ✅ Incluye Brand Manual, Brief Estratégico, logos | ✅ Token renovado |
| **Obsidian Vault** | `/root/obsidian-vault/empresas/la-carolina/` | Notas, análisis, reportes por subcarpeta | ✅ |

**Ruta local datos reclutamiento CSV:**
`/root/obsidian-vault/empresas/la-carolina/dashboard-redes-ads-reclutamiento/data/`

**Datos estructurados de flota (placas, internos, marcas):**
`/root/metrocaribe/public/data/flota-foton-yutong-placas-internos.md`

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

## 🔗 Links Rápidos

- GitHub nexus-os: `github.com/lanayagiraldo-a11y/nexus-os`
- GitHub ia-masters-os: `github.com/lanayagiraldo-a11y/ia-masters-os`
- GitHub metrocaribe (datos LC): `github.com/lanayagiraldo-a11y/metrocaribe`
- Supabase Studio #1: `supabase.com/dashboard/project/exloqxhnzplovndcjale`
- Supabase Studio #2: `supabase.com/dashboard/project/lqeddrpbwunzcyjxuiei`

---

## ⚠️ Notas Importantes

1. **Dashboard cron 6am:** 🛑 Detenido por orden de Liliana. No reactivar sin nueva orden.
2. **GEMA MySQL:** Password expirado. Preguntar antes de intentar reconectar.
3. **OneDrive corp:** ✅ Token renovado. Brand manual, brief, logos descargados.
4. **GESTIVO API key:** Guardada en sesiones previas de Telegram. Preguntar a Liliana si se pierde.
5. **Telegram file limit:** archivos >20MB no se pueden enviar. Usar OneDrive/Drive como alternativa.
6. Cuando Liliana diga "cron dashboard" o "actualiza dashboard", preguntar primero antes de hacer deploy.

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
