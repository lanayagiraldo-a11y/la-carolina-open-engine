---
name: agents-financieros-la-carolina
description: "Sistema multi-agente financiero para La Carolina y empresas del grupo: análisis para accionistas/junta, datos financieros, contabilidad Colombia, revisoría fiscal, benchmarking sectorial, viabilidad de proyectos y auditoría de integridad. Úsalo para cualquier análisis financiero, contable, tributario, viabilidad, junta, Aroca/contabilidad o revisión de cifras de La Carolina."
version: 1.0.0
author: LaCarolinaTPC + Hermes Agent
license: private
metadata:
  hermes:
    tags: [la-carolina, finanzas, contabilidad, junta, accionistas, transporte, colombia, aroca]
    source_repo: https://github.com/LaCarolinaTPC/la-carolina-skills/tree/main/agents-financieros
---

# Agents Financieros La Carolina

## Cuándo usar esta skill

Usa esta skill cuando Liliana pida cualquiera de estos trabajos:

- analizar estados financieros, balances, P&G, flujo de caja o indicadores;
- preparar informe financiero para junta directiva o accionistas;
- revisar información contable/financiera de La Carolina, AG, AG Constructora, Interstaff u otra empresa del grupo;
- cruzar información financiera con operación de transporte;
- revisar pendientes con contabilidad, financiera, Aroca, revisor fiscal o asesores;
- evaluar viabilidad de proyectos, buses, inversión, financiación o expansión;
- detectar riesgos tributarios, contables, societarios o de cumplimiento;
- preparar agenda, matriz de pendientes, acta o correo para reunión financiera/legal-contable;
- preparar un **briefing ejecutivo para reunión con gestión interna** (Edith Royero, John Guerrero, Helmuth, etc.) que cruce finanzas + operación + patrimonio familiar + temas Colombia en un solo documento, organizado por responsable y con preguntas accionables por persona;
- convertir cifras técnicas en lenguaje claro para accionistas.

## Principio central

El sistema debe entregar análisis financiero **claro, verificable y accionable para accionistas**. No basta con calcular indicadores: debe explicar qué significan, qué decisión habilitan, qué dato falta y quién debe actuar.

## Modo de operación

Antes de producir, clasifica la solicitud:

### 1. Modo Rápido

Usar cuando Liliana pide una respuesta corta, revisión puntual o “solo dime qué falta”.

Ejemplos:
- “¿Qué hace falta para la reunión?”
- “¿Está bien este indicador?”
- “Hazme un resumen de estos pendientes.”
- “Dame una lista para mandarle a Aroca.”

Reglas:
- No actives todo el sistema multi-agente.
- Responde con los datos disponibles.
- Señala límites o datos faltantes.
- Máximo 5–8 bullets, salvo que pida documento/correo.
- Ofrece profundizar si hace falta.

### 2. Modo Profundo

Usar cuando pide informe para junta, accionistas, diagnóstico completo, viabilidad o análisis formal.

Flujo:
1. Inspeccionar archivos/datos disponibles antes de preguntar.
2. Hacer solo las preguntas críticas que falten.
3. Elegir especialistas necesarios.
4. Procesar datos y calcular indicadores.
5. Revisar contabilidad / tributario / sector / viabilidad según aplique.
6. Auditar integridad antes de entregar.
7. Traducir a lenguaje de accionista.
8. Entregar informe, matriz de decisiones y pendientes por persona.

### 3. Modo Reunión con asesores

Usar para reuniones con Aroca, contabilidad, financiera, revisor fiscal, bancos, abogados o asesores.

Salida recomendada:
- agenda ejecutiva;
- lista de pendientes por tema;
- evidencia/documento asociado;
- qué falta;
- responsable interno;
- responsable externo;
- fecha solicitada;
- preguntas para la reunión;
- correo previo o acta posterior si Liliana lo pide.

## Roles del sistema

Los perfiles originales están guardados en `references/agents/`.

### Orquestador

- `analista-financiero-senior.md`
- Punto de entrada para análisis financiero.
- Decide modo rápido/profundo.
- Coordina especialistas.
- Consolida y traduce al lenguaje de accionistas.

### Especialistas

- `analista-datos-financieros.md`: extracción, limpieza, cálculo y validación de datos financieros.
- `contador-colombia.md`: NIIF, PUC, calidad contable, clasificación de cuentas y revelaciones.
- `revisor-fiscal-colombia.md`: riesgos fiscales, DIAN, retenciones, IVA, renta, exógena, cumplimiento societario/laboral.
- `analista-sectorial.md`: benchmarking Colombia, sector transporte, competidores, fuentes públicas.
- `analista-viabilidad-proyectos.md`: VPN, TIR, WACC, payback, sensibilidad, escenarios.
- `auditor-integridad-financiera.md`: verificación matemática, consistencia, fórmulas, sesgos y “no verificable”.

## Rutas reales de trabajo para Hermes/Liliana

Usa estas rutas en este entorno:

- Entregables para Telegram: `/root/.hermes/document_cache/`
- Bóveda Obsidian: `/root/obsidian-vault/`
- Notas La Carolina: `/root/obsidian-vault/empresas/la-carolina/`
- Repositorios: `/root/repos/`
- Dashboards/HTML públicos: publicar en Netlify solo si Liliana lo pide o si el entregable necesita link.

No uses por defecto `~/AI/...` salvo que exista o Liliana lo indique. Si los perfiles originales mencionan `~/AI/data-financiera/...`, adapta a las rutas reales de Hermes.

## Regla obligatoria de fuente, corte y confianza

Toda cifra material debe clasificarse así:

```md
| Dato | Valor | Fuente | Fecha de corte | Unidad | Tipo | Confianza |
|---|---:|---|---|---|---|---|
| ... | ... | Excel / correo / repo / estimación | YYYY-MM-DD | COP / miles / millones / USD | real / supuesto / estimado | alta / media / baja |
```

Nunca mezcles cifras reales, supuestos y estimaciones sin marcarlo.

## Privacidad y seguridad financiera

- No publiques estados financieros, NITs completos, cuentas bancarias, credenciales, datos personales sensibles o información de socios en links públicos sin autorización explícita.
- Para HTML público, anonimiza o resume lo sensible.
- Reemplaza credenciales/tokens/claves por `[REDACTED]`.
- Si el análisis es legal/tributario, presenta riesgos y preguntas, pero no lo marques como concepto formal firmado.

## Flujo A — Análisis financiero estándar

1. Identificar empresa, periodo, fuente y objetivo.
2. Extraer Balance, P&G y Flujo si existen.
3. Validar cuadres básicos.
4. Calcular indicadores financieros: liquidez, rentabilidad, endeudamiento, eficiencia y caja.
5. Para La Carolina, agregar KPIs operativos de transporte: ingreso por bus, ingreso por km, combustible/ingresos, mantenimiento/bus, rotación de conductores, costo laboral/ingresos, ocupación y edad de flota.
6. Comparar con periodo anterior si existe.
7. Identificar banderas rojas.
8. Auditar integridad.
9. Entregar lectura para accionistas: qué significa, qué decisión tomar, quién debe actuar.

## Flujo B — Viabilidad de proyecto o inversión

1. Definir proyecto y objetivo de decisión.
2. Levantar supuestos: inversión inicial, ingresos, costos, plazo, financiación, impuestos, CAPEX/OPEX.
3. Separar supuestos reales vs estimados.
4. Construir flujo de caja libre.
5. Calcular WACC si aplica.
6. Calcular VPN, TIR/TIRM, payback y punto muerto.
7. Hacer sensibilidad y escenarios.
8. Comparar con benchmark sectorial cuando sea posible.
9. Emitir veredicto: viable / viable condicionado / no viable / no concluyente.
10. Listar condiciones mínimas para decidir.

## Flujo C — Reunión financiera/contable/legal-contable

1. Inventariar documentos, correos, repos y archivos revisados.
2. Agrupar por frente: contable, financiero, tributario, societario, legal, operativo.
3. Separar: hecho/documentado, en proceso, faltante y no verificable.
4. Crear matriz:

```md
| Tema | Estado | Evidencia | Qué falta | Responsable | Fecha solicitada | Prioridad |
|---|---|---|---|---|---|---|
```

5. Preparar correo previo o agenda si Liliana lo pide.
6. Después de la reunión, preparar acta con decisiones y próximos pasos.

## Flujo F — Briefing ejecutivo para reunión con gestión interna (Edith, John, Helmuth, etc.)

Usa este flujo cuando Liliana pida preparar una reunión con **gestión interna de La Carolina**: gerencia financiera, gerencia operativa, tesorería, contabilidad, operaciones, Gestión Humana — **no** asesores externos ni abogados. El briefing cruza finanzas + operación + patrimonio familiar en un solo documento.

### Fuentes de información

**Siempre** consultar en paralelo (no secuencialmente):

1. **Historial de sesiones Hermes:** buscar sesiones recientes con los nombres de los participantes y "La Carolina" mediante `session_search` para capturar acuerdos, decisiones y pendientes de reuniones previas.
2. **Obsidian vault:** leer `pendientes-activos.md` (fuente de verdad de pendientes clasificados), las daily notes recientes, y las notas específicas del área (flujo de caja, actas de reunión, tablero de ahorros, actas de afiliados, notas de patrimonio).
3. **Actas de reuniones previas:** notas en `empresas/la-carolina/` como actas de flujo de caja, reuniones de directivos, reuniones de afiliados, ahorros y control.
4. **Notas de patrimonio:** `proyectos/organizacion-patrimonio-familiar/` para items Colombia como Cartagena, Ensis, protocolo familiar.

### Estructura del briefing

Organizar en este orden:

#### 1. LA CAROLINA — Flujo de caja y créditos 🎯 PRIORIDAD
- Tabla de fuentes de caja por responsable: monto, gestión, último estado conocido
- Proyecciones aprobadas (timbradas, escenarios)
- Riesgos de liquidez explícitos

#### 2. LA CAROLINA — Operaciones y ahorros
- Frentes de ahorro por área (combustible ✅ cerrados, software, llantas, lavado, urea)
- Afiliados: próxima reunión, temas críticos (programación, liquidación, bonos, hojas de vida)
- Marketing: métricas, embudo reclutamiento, campañas, CarolinaSAS

#### 3. PATRIMONIO FAMILIAR — Colombia
- Cartagena: lote, expropiación, reunión abogados, registro
- Ensis: traspaso acciones, firmas pendientes, Superintendencia
- Protocolo de familia: estado con Aroca/Ivón, FIPs, DC Capital/avales
- Reunión jurídica mensual

#### 4. OTROS TEMAS COLOMBIA
- Yutong: estado de buses, pagaré, nacionalización 4° bus
- K12 Homeschooling: fechas, pagos
- Otros según contexto

### Reglas de formato para el briefing

1. **Organizar por persona responsable.** Cada bloque debe tener claro qué le corresponde a Edith, a John, o a ambos. Ejemplo: los créditos tienen columna de responsable.
2. **Prioridad visual:** 🔴 = crítica/urgente, 🟠 = semana, 🟡 = seguimiento, 🔵 = informativo. Marcar con 🎯 los items prioritarios de la reunión.
3. **Tablas para datos comparativos:** créditos, ahorros por área, pendientes con fecha. Preferir tablas markdown sobre párrafos.
4. **Cada bloque termina con preguntas accionables** específicas para cada persona: qué preguntarle a Edith, qué preguntarle a John.
5. **Tabla resumen ejecutivo al final:** prioridad, tema, responsable(s), próxima acción. Una mirada para la reunión.
6. **Multi-source cross-reference:** no depender de una sola nota — cruzar session_search + pendientes-activos + daily + actas de reunión. Si hay discrepancia entre fuentes, marcarlo.
7. **Estado de cada gestión financiera:** no solo si está "pendiente", sino el último hito conocido (radicado, aprobado, en contrato, desembolsado) y el siguiente paso concreto.

## Flujo D — Auditoría de integridad

Antes de entregar un análisis formal:

1. Verificar cuadres contables.
2. Recalcular fórmulas clave.
3. Confirmar que la misma cifra no aparece con valores distintos.
4. Revisar semáforos vs umbrales.
5. Marcar cualquier cifra no verificable.
6. Detectar sesgos: confirmación, cherry-picking, atribución falsa, exceso de confianza.
7. No entregar como final si hay fallos críticos sin explicar.

## Flujo E — Recaudo Transmetro / TPC / FET-FQ

Usa este flujo cuando Liliana traiga tablas o fotos de recaudo de Transmetro, FET, FQ, TPC o empresas operadoras.

1. Extraer primero las cifras visibles y declarar si vienen de foto/OCR, Excel o archivo fuente. Si una columna proyectada no se ve, no la inventes.
2. Para movilidad estimada desde la columna verde “proyectado a pagar”, usar la fórmula que Liliana validó:
   - `movilidad estimada = proyectado_a_pagar / 200`
3. Para eficiencia de recaudo por periodo:
   - `% recaudo = recaudado / proyectado_a_pagar`
   - `pago real por pasajero = 200 * % recaudo`
4. Para cuotas FET/FQ, separar dos escenarios obligatorios:
   - **Escenario 100% recaudo:** `movilidad necesaria = cuota / 200`
   - **Escenario recaudo real:** `movilidad necesaria = cuota / (200 * % recaudo_promedio)`
5. Si la usuaria da cuotas mensuales como “2,300,000 millones”, interpretar por contexto financiero como `$2.300.000.000` y aclarar el supuesto; recalcular si confirma otra unidad.
6. Entregar en formato ejecutivo: periodo, recaudado, proyectado, movilidad, % recaudo, pago real/pasajero y conclusión. Evitar tablas largas en Telegram; usar bullets claros.
7. Análisis mínimo: detectar meses atípicos, brecha vs proyectado, si el problema es movilidad o eficiencia de recaudo, y cuánto tendría que subir el recaudo para reducir la movilidad necesaria.

Referencia de ejemplo: `references/transmetro-fet-fq-recaudo.md`.

## Entregables durante dictado / notas en vivo

Cuando Lily esté construyendo un informe financiero, de flujo de caja, junta o reunión y diga que seguirá dando notas:

- Trabaja en **modo acumulación**: sintetiza, ordena y confirma lo entendido, pero **no generes ni envíes PDF/DOCX** hasta que Lily lo pida explícitamente.
- Si ya existe un borrador o PDF anterior, no lo reenvíes ni lo regeneres por iniciativa propia; espera una señal como “ahora sí dame el PDF”, “ciérralo”, “imprime” o “documento final”.
- Mantén las notas con estructura accionable: cifras, preguntas, decisiones, responsables y pendientes, marcando qué es dato real vs. supuesto.
- Si Lily corrige el tema (“no es Aroca, es Carolina flujo de caja”), cambia inmediatamente el marco del documento y deja de arrastrar la categoría anterior.

## Estructura recomendada de entrega

```md
## En una frase
[Conclusión principal en lenguaje llano]

## Lo que encontramos
- Hallazgo 1 + cifra + significado
- Hallazgo 2 + cifra + significado

## Tablero de salud
| Indicador | Valor | Tendencia | Qué significa | Semáforo |

## Decisiones para junta / accionistas
| Decisión | Recomendación | Riesgo si no se hace | Responsable | Fecha sugerida |

## Pendientes por persona
- Edith: ...
- Jorge: ...
- Néstor: ...
- Aroca: ...

## Lo que todavía no se puede concluir
- ...

## Próximo paso
- ...
```

### Entregables imprimibles para reunión

Cuando Liliana pida “PDF”, “matriz”, “bulletpoints”, “tabla fácil de imprimir” o “documento imprimible” en una conversación donde hay varios frentes abiertos, **reconfirmar mentalmente el tema activo antes de generar**. No reutilices el último HTML/PDF si el hilo cambió de tema.

- Si la usuaria corrige “ya no es tema X, es tema Y”, descartar inmediatamente el entregable anterior como contexto de producción y rehacer el documento con título, matriz y contenido centrados en Y.
- Para La Carolina flujo de caja, el PDF imprimible debe priorizar: situación base de caja, decisiones inmediatas, matriz de ahorros/caja, preguntas para reunión y entregables por responsable. Mantenerlo corto: 1–3 páginas si no pide informe largo.
- Evitar mezclar pendientes legales/Aroca con matrices financieras de caja aunque ambas pertenezcan a La Carolina; separar por frente y nombrar el archivo claramente (`matriz_carolina_flujo_caja...`, no `aroca...`).
- Si se adjunta un HTML operativo y la plataforma rechaza `.html`, convertir a PDF o a formato soportado en vez de reenviar el HTML.

## Mejoras ya incorporadas sobre el repo original

1. Corrección de contradicción entre Modo Rápido y “4 preguntas obligatorias”: las 4 preguntas aplican solo en Modo Profundo.
2. Adaptación de rutas a Hermes/Telegram/Obsidian/Netlify.
3. Bloque obligatorio de fuente, corte, unidad, tipo y confianza.
4. Bloque explícito de privacidad financiera.
6. Modo especial de reunión con asesores.
7. Matriz final de decisiones y matriz de pendientes.
8. Sección "lo que todavía no se puede concluir".
9. Flujo F: briefing ejecutivo para reunión con gestión interna de La Carolina — multi-source, por persona, preguntas accionables.

## Archivos de referencia

Carga estos archivos cuando necesites el detalle exacto de cada rol:

- `references/agents/analista-financiero-senior.md`
- `references/agents/analista-datos-financieros.md`
- `references/agents/contador-colombia.md`
- `references/agents/revisor-fiscal-colombia.md`
- `references/agents/analista-sectorial.md`
- `references/agents/analista-viabilidad-proyectos.md`
- `references/agents/auditor-integridad-financiera.md`

## Posicionamiento ante Liliana

- Presentar este sistema como **metodología financiera de trabajo para Hermi**, no como agentes autónomos.
- Los archivos de agentes en `references/agents/` son perspectivas o roles de análisis, no bots vivos que actúan solos.
- Si Liliana pregunta por qué se habla de skill/agente, explicar corto: “queda como metodología reutilizable que Hermi carga cuando me pidas análisis financieros; no actúa sola”.
- Ver detalle en `references/naming-and-user-facing-positioning.md`.

## Preferencias operativas de Liliana para informes financieros

- Si Liliana está dictando notas, compartiendo capturas o diciendo que “seguirá dando notas”, **no generes PDF ni entregable final** hasta que pida explícitamente “dame el PDF”, “ya puedes hacerlo”, “ciérralo” o equivalente. Mientras tanto, acumula insumos y confirma brevemente cómo los estás incorporando.
- Para informes de **La Carolina / flujo de caja**, mantener el análisis **mes a mes** cuando haya timbradas/movilidad: enero–diciembre, comparativo contra años anteriores, variación y lectura de caja por mes. El acumulado anual va solo como resumen secundario.
- Si pide preguntas para reunión de flujo de caja, prioriza un set corto de 8–10 preguntas accionables antes que listas largas: caja disponible/comprometida, obligaciones 4 semanas, recaudo real vs presupuesto, timbradas mes a mes, rutas/buses/propietarios que explican variación, top gastos de caja, ahorros reales vs proyectados y escenarios de cierre.
- Si corrige el tema del documento (por ejemplo “no es Aroca, es Carolina flujo de caja”), descarta la línea equivocada y rehace el entregable/estructura bajo el tema correcto; no reutilices PDFs/links de otro frente.

## Checklist antes de entregar

- [ ] ¿Está claro si es modo rápido, profundo o reunión?
- [ ] ¿Cada cifra material tiene fuente/corte/unidad/tipo/confianza?
- [ ] ¿Se separan hechos, supuestos y estimaciones?
- [ ] ¿Hay pendientes por persona y no tareas abstractas?
- [ ] ¿Se explica en lenguaje de accionista?
- [ ] ¿Se identificó qué no se puede concluir?
- [ ] ¿Se protegió información sensible?
- [ ] ¿Se verificó el archivo/link/documento entregado?
