# FQ / Superpolo — Análisis de capacidad de pago

## Data sources (all from AMB)

| Source | Key data |
|---|---|
| `INFORME COMITE FQ MAYO 2026.pdf` | Recaudo FQ ene-may 2026 por empresa, cartera total, difícil cobro, saldo, pagos a Superpolo |
| `PRESENTACION COMITE FQ 03 FEBRERO 2026.pdf` | Recaudo FQ mensual 2025 ($12,730M total) |
| `DANE ETUP IVtrim2025.pdf` | Movilidad TPC: IV trim 2024 vs IV trim 2025 (-10.3%, 4.307M pasajeros perdidos) |
| `Plan de pagos Superpolo` | Capital inicial $56,148M, cuota $1,983M/mes, 24 cuotas (ene-2026 a dic-2027) |
| `Presentacion_TPC_Barranquilla_2026.pptx` (John Guerrero) | +4,000 motocarros AMB 2024, $0 FET/FQ, sin SOAT/revisión/habilitación |

## Key numbers

| Concept | Value |
|---|---|
| Total recaudo FQ 2025 | $12,730,346,051 |
| Promedio mensual 2025 | $1,060,862,171 |
| Total recaudo ene-may 2026 | $7,463,468,749 |
| Promedio mensual ene-may 2026 | $1,492,693,750 |
| Cuota mensual Superpolo | $1,983,423,393 |
| Saldo FQ a mayo 2026 | $9,284,928,900 |
| Capital inicial Superpolo | $56,148,100,000 |
| Pagado a Superpolo (a may/26) | $15,060,000,000 |
| Cuotas restantes (jun-26 a dic-27) | 19 |

## The -23.6% metric

**The -23.6% is in PASSENGERS, not in revenue ($).** The rate doubled from $100→$200, so recaudo INCREASED +40.7%:

- 2025: 10.6M passengers/month × $100 = $1,061M/month
- 2026: 8.1M passengers/month × $200 = $1,493M/month (+40.7%)

Always label -23.6% as "dato interno AMB/FQ" — it's from FQ liquidation reports, NOT DANE.

## Answer pattern: ONE clear answer first

> **Short answer:** No, barely. Shortfall of ~$39M.
>
> With the current -23.6% passenger decline and $200/passenger rate, FQ generates $1,493M/month, leaving a deficit of $490M/month vs the $1,983M cuota. The $9,285M reserve covers 19 months — right up to the last payment — but falls short by ~$39M.
>
> **If decline worsens 5%:** shortfall grows to $1,457M.

### Calculation
```
Total available: $9,285M + 19×$1,493M = $37,646M
Total needed:   19×$1,983M = $37,685M
Shortfall:      ~$39M
```

## Distinctions (NEVER mix these)

| Concept | Key distinction |
|---|---|
| TPC | Traditional bus. Its paying passengers feed the FQ |
| Transmetro | BRT. Separate passengers, revenue, evasion |
| FQ | Fondo de Calidad. $200/passenger (2026). Pays Superpolo |
| FET | Fondo de Estabilización Tarifaria. Different fund. Different data |

## Recaudo 2025 variability

| Month | $M | Note |
|---|---|---|
| Ene | 773 | |
| Feb | 644 | Low |
| Mar | 653 | Low |
| Abr | 866 | |
| May | 912 | |
| Jun | 1,465 | Spike |
| Jul | 2,463 | Double liquidation |
| Ago | 915 | |
| Sep | 1,091 | |
| Oct | 1,113 | |
| Nov | 1,055 | |
| Dic | 778 | Low |

**Rule:** Always note "Julio incluye dos liquidaciones." Picos include arrears, not seasonal travel.

## Data verification: chart vs source

| Month | Chart | Actual PDF | Issue |
|---|---|---|---|
| Abril 2026 | ~$1,400M | $2,509M | Chart used average, not actual |
| Mayo 2026 | ~$1,500M | $981M | Chart used average, not actual |

**Rule:** For past months, use real data from PDF; only use averages for projections.

## Verified data from John Guerrero's PPTX

| Data point | Source |
|---|---|
| +4,000 motocarros ilegales (AMB 2024) | Presentación TPC slide 2 |
| $0 FET, FQ, SOAT, revisión, habilitación | Presentación TPC slide 4 |
| Tarifas artificialmente bajas (evaden costos regulatorios) | Presentación TPC slide 4 |
| Crecimiento nacional motos +18% (2024 vs 2023) | Fenalco-ANDI |

## Slide structure

| # | Title | Key content |
|---|---|---|
| 1 | Title | -33.1%, -10.3%, -23.6% |
| 2 | Demanda fuera del sistema | +4,000 motocarros, $0 FET/FQ |
| 3 | Deterioro estructural | TPC -33.1%, SITM -54.1%, Moto +117% |
| 4 | Más flota, menos pasajeros | Transmetro +11.1% flota, -10.6% pasajeros |
| 5 | Golpe al FQ | Chart recaudo 2025 + $430.7M impact |
| 6 | Presión financiera 2026 | Cartera $2,561M, difícil cobro $1,428M, saldo $9,285M |
| 7 | El FQ pierde capacidad | -23.6% movilidad → -$501M/mes |
| 8 | La reserva entra en estrés | Chart 3 escenarios |
| 9 | La crisis en cifras | Solo datos confirmados |
| 10 | Factores de la crisis | Incluye motos eléctricas $0 FET/FQ |
| 11 | Acciones en marcha | SIBUS, 100 buses, operativos |
| 12 | Conclusión | Narrative close |

## Text corrections (from CONTINUIDAD)

| Before | After |
|---|---|
| "Aquí está uno de los mensajes más importantes..." | "Uno de los puntos más relevantes de los datos es que la incorporación de más buses no se tradujo en un aumento estructural de pasajeros registrados. Al mismo tiempo, el TPC perdió movilidad, lo que evidencia una contracción del sistema formal pagante." |
| "Ausencia de operativos" | "La informalidad persiste pese a los operativos reportados; la información pública disponible no permite medir todavía si su cobertura y continuidad son suficientes frente al tamaño del problema." |
| "probablemente" / "explicación más probable" | "Los datos muestran pérdida de demanda del sistema formal pagante y factores de dispersión, pero no permiten asignar porcentajes a evasión, mototaxi, taxi, moto particular u otros modos." |
