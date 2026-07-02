# Transmetro / TPC / FET-FQ — cálculo de recaudo y movilidad

## Contexto de la sesión

Liliana compartió una foto con recaudo mensual de empresas operadoras FET. La columna verde era “proyectado a pagar”; Liliana corrigió que para estimar movilidad se debe dividir esa columna por COP $200.

## Fórmulas validadas

- `movilidad estimada = proyectado_a_pagar / 200`
- `% recaudo = recaudado / proyectado_a_pagar`
- `pago real por pasajero = 200 * % recaudo`
- `movilidad necesaria 100% recaudo = cuota_mensual / 200`
- `movilidad necesaria con recaudo real = cuota_mensual / (200 * % recaudo_promedio)`

## Patrón de entrega recomendado

Para cada periodo:

- Recaudado: COP
- Proyectado a pagar: COP
- Movilidad estimada: pasajeros
- % de recaudo
- Pago real equivalente por pasajero

Luego entregar:

- promedio ponderado del periodo total: `sum(recaudado) / sum(proyectado)`
- lectura ejecutiva: meses críticos, meses buenos, brecha total y si el cuello de botella es movilidad o eficiencia de recaudo.

## Ejemplo de lectura con datos de la sesión

Fila total general visible enero-mayo 2026:

- Enero: recaudado 1.275.107.220; proyectado 2.069.126.791; % recaudo 61,63%; movilidad 10.345.634.
- Febrero: recaudado 706.290.057; proyectado 1.808.891.642; % recaudo 39,05%; movilidad 9.044.458.
- Marzo: recaudado 1.728.769.315; proyectado 1.948.001.198; % recaudo 88,75%; movilidad 9.740.006.
- Abril: recaudado 1.586.824.082; proyectado 1.786.466.060; % recaudo 88,82%; movilidad 8.932.330.
- Mayo: recaudado 1.816.477.704; proyectado 2.337.634.862; % recaudo 77,71%; movilidad 11.688.174.

Promedio ponderado enero-mayo:

- Recaudado total: 7.113.468.378
- Proyectado total: 9.950.120.553
- Movilidad total estimada: 49.750.603
- % promedio ponderado de recaudo: 71,49%
- Pago real promedio por pasajero: COP $142,98 de los $200 esperados.

## Cuotas FET/FQ usadas en la sesión

Interpretación contextual de Liliana:

- FET: COP $2.300.000.000 mensuales.
- FQ: COP $2.000.000.000 mensuales.

Con recaudo perfecto:

- FET: 11.500.000 pasajeros/mes.
- FQ: 10.000.000 pasajeros/mes.
- FET + FQ: 21.500.000 pasajeros/mes.

Con recaudo real promedio 71,49%:

- FET: ~16.085.878 pasajeros/mes.
- FQ: ~13.987.720 pasajeros/mes.
- FET + FQ: ~30.073.598 pasajeros/mes.

## Pitfalls

- No promediar porcentajes simples si los meses tienen proyectados distintos; usar promedio ponderado: `sum(recaudado) / sum(proyectado)`.
- No incluir junio parcial si no hay proyectado visible, salvo que se declare como corte parcial.
- En Telegram no usar tablas extensas: las tablas se ven mal; preferir bullets por periodo.
- Si el dato viene de foto, marcar confianza media y recomendar reconstruir en Excel para análisis empresa por empresa.
