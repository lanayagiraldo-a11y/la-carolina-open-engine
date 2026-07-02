# Mobile-compatible SVG charts

Chart.js canvas charts **do not render** in Telegram's embedded browser or many mobile webviews. Users report "no se ven las gráficas" / "images not showing". Fix: replace the two problematic canvas charts with inline SVG.

## Which charts to replace

Target the **last 2 charts** in the grid — typically "timbradas por bus (promedio mensual)" and "tendencia mensual por flota". The first 4 Chart.js charts (bar, horizontal bar, per-group bar, doughnut) render fine in desktop browsers but may also fail on mobile. In practice, replace only the last 2 if the first 4 survive.

## Bar chart (inline SVG)

Replace `<canvas id="chartComparativa"></canvas>` with:

```html
<style>
.inline-chart{display:flex;align-items:flex-end;gap:14px;height:110px;margin-top:4px;padding:0 4px}
.inline-bar{display:flex;flex-direction:column;align-items:center;flex:1;height:100%;justify-content:flex-end}
.inline-bar .bar{width:100%;border-radius:4px 4px 0 0;min-height:4px;transition:height .3s}
.inline-bar .lbl{font-size:8px;color:var(--muted);margin-top:4px;text-align:center;font-weight:700;text-transform:uppercase}
.inline-bar .val{font-size:7px;color:var(--white);font-weight:700;margin-bottom:2px}
</style>
<div class="inline-chart">
  <div class="inline-bar"><div class="val">13,667</div><div class="bar" style="height:110px;background:var(--green)"></div><div class="lbl">Yutong</div></div>
  <div class="inline-bar"><div class="val">11,924</div><div class="bar" style="height:96px;background:var(--teal)"></div><div class="lbl">Fotón</div></div>
  <div class="inline-bar"><div class="val">5,150</div><div class="bar" style="height:41px;background:var(--muted)"></div><div class="lbl">Chev.</div></div>
</div>
```

Calibrate bar heights: `bar_height = round(value / max_value * max_px)`. Scale the tallest bar to fill `max_px` (typically 110px for the chart-box).

## Line chart (inline SVG)

Replace `<canvas id="chartTendencia"></canvas>` with:

```html
<div class="line-legend">
  <span><span class="swatch" style="background:var(--green)"></span>Yutong</span>
  <span><span class="swatch" style="background:var(--teal)"></span>Fotón</span>
  <span><span class="swatch" style="background:var(--muted)"></span>Chevrolet</span>
</div>
<div class="line-chart">
  <svg viewBox="0 0 280 120" xmlns="http://www.w3.org/2000/svg">
    <!-- Grid lines -->
    <line x1="40" y1="108" x2="270" y2="108" stroke="#3a3a3a" stroke-width="1"/>
    <line x1="40" y1="36" x2="270" y2="36" stroke="#3a3a3a" stroke-width="0.5" stroke-dasharray="3,3"/>
    <line x1="40" y1="72" x2="270" y2="72" stroke="#3a3a3a" stroke-width="0.5" stroke-dasharray="3,3"/>
    <!-- Data line + dots per series -->
    <polyline points="55,106 140,48 225,59" fill="none" stroke="#4caf50" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"/>
    <circle cx="55" cy="106" r="3" fill="#4caf50"/><circle cx="140" cy="48" r="3" fill="#4caf50"/><circle cx="225" cy="59" r="3" fill="#4caf50"/>
    <!-- X-axis labels -->
    <text x="55" y="116" text-anchor="middle" fill="#8a8678" font-size="7">Abril</text>
    <text x="140" y="116" text-anchor="middle" fill="#8a8678" font-size="7">Mayo</text>
    <text x="225" y="116" text-anchor="middle" fill="#8a8678" font-size="7">Junio</text>
    <!-- Y-axis labels -->
    <text x="34" y="108" text-anchor="end" fill="#555" font-size="6">0</text>
    <text x="34" y="72" text-anchor="end" fill="#555" font-size="6">18K</text>
    <text x="34" y="36" text-anchor="end" fill="#555" font-size="6">37K</text>
  </svg>
</div>
```

Calibrate Y-coordinates: data range → viewport range. If data = [0, 36698] and viewport Y = [108, 10], then `y = 108 - (value / 36698 * (108 - 10))`.

## Pitfalls

- **Remove orphan JS**: After replacing the canvas with SVG, delete the corresponding `new Chart(document.getElementById(...))` calls from the inline `<script>` block. Otherwise Chart.js throws a null-reference error when it can't find the canvas, which may abort the rest of the script block.
- **Missing `</script>`**: If the second script block had no closing `</script>` tag before `</body>`, remove the entire orphan block. The first script block (charts 1-4) already properly ends with `</script>`.
- **Cache**: After deploying, do a hard refresh (`?v=N`) — Cloudflare/Netlify cache may serve the old version.
- **No external deps needed**: SVG charts work without Chart.js CDN, so they render even on flaky connections.
