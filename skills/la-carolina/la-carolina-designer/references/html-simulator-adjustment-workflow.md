# Ajustes a simuladores/reportes HTML de La Carolina / Transmetro

Uso: cuando Liliana pide modificar un HTML existente de simulador, reporte o dashboard operativo, especialmente si dice “haz una copia” o “no cambies el inicial”.

## Flujo recomendado

1. **Preservar original**
   - Identifica el HTML base más reciente en `~/.hermes/document_cache/` o la ruta indicada.
   - Crea una copia con nombre claro: `*_COPIA_AJUSTADA_Liliana_FECHA.html`.
   - Calcula/guarda hash o tamaño del original y de la copia para poder afirmar que el original quedó intacto.

2. **Ubicar estructura antes de editar**
   - En HTML grandes con JS embebido, las búsquedas exactas pueden fallar por acentos/nombres distintos.
   - Inspecciona secciones por rangos de líneas y busca IDs/funciones (`panel`, `tab`, `showTab`, `render*`, `calc*`).
   - Si las pestañas dependen del orden DOM (`showTab(i)` con `.tab` y `.panel`), al quitar una pestaña también debes reindexar botones y paneles. Pitfall común: dejar pestañas invisibles o inputs no visibles porque el índice quedó corrido.

3. **Cambios de claridad para Liliana**
   - Cuando una gráfica combine años, agregar una nota visible de “Periodo de datos”: qué es histórico, qué es real parcial y qué es proyección.
   - Si hay cajas financieras o siglas, explicar en lenguaje ejecutivo qué bolsillo representa cada caja; no asumir que FET/FQ/TPC/TM son obvios.
   - Para preguntas tipo “¿podemos pagar?” o “¿cuándo se usa la reserva?”, responder dentro del HTML con regla clara: la reserva se usa cuando recaudo mensual < cuota; deja de usarse cuando recaudo mensual ≥ cuota.
   - Si el usuario pide “mes a mes”, agregar tabla narrativa/KPIs por mes, no solo gráfico acumulado.

4. **Verificación mínima antes de entregar**
   - Extrae scripts embebidos y corre `node --check` para detectar errores de sintaxis.
   - Sirve el HTML con `python3 -m http.server --directory ...` y prueba con Playwright en Chromium headless.
   - Verifica: número de pestañas, que cada pestaña abre, que no hay `pageerror`/console error, y que inputs/calculadoras nuevas son visibles y producen texto esperado.
   - Si browser_navigate se cuelga con archivos HTML grandes, usa Playwright directamente desde Python; no conviertas eso en una regla negativa sobre el browser.

5. **Entrega por Telegram**
   - Enviar activamente el HTML desde `/root/.hermes/document_cache/` con `send_message` y, si se quiere descargable, `[[as_document]]`.
   - En la respuesta final, resumir cambios y verificaciones reales, no solo decir “listo”.

## Mini-script de verificación Playwright

```python
from playwright.sync_api import sync_playwright
url = 'http://127.0.0.1:8765/archivo.html'
with sync_playwright() as p:
    browser = p.chromium.launch(headless=True)
    page = browser.new_page()
    logs = []
    page.on('console', lambda msg: logs.append((msg.type, msg.text)))
    page.on('pageerror', lambda exc: logs.append(('pageerror', str(exc))))
    page.goto(url, wait_until='domcontentloaded', timeout=20000)
    page.wait_for_timeout(1000)
    for i in range(page.locator('.tab').count()):
        page.locator('.tab').nth(i).click(timeout=5000)
        page.wait_for_timeout(300)
    print('tabs', page.locator('.tab').count())
    print('errors', [l for l in logs if l[0] in ('error','pageerror')])
    browser.close()
```
