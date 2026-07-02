# Per-bus driver concentration analysis

## Key insight

Each bus typically has **1-2 main drivers** who perform **90%+ of the trips**. The rest are relief drivers who appear sporadically.

## Calculation

```python
for placa in all_plates:
    vp = [v for v in all_viajes if v.get('placa','') == placa]
    cond_por_bus = Counter()
    for v in vp:
        cn = v.get('conductor_nombre','').strip()
        if cn: cond_por_bus[cn] += 1
    
    total_v = sum(cond_por_bus.values())
    top3 = cond_por_bus.most_common(3)
    top3_pct = sum(c for _, c in top3) / total_v * 100
    menos10 = sum(1 for c in cond_por_bus.values() if c < 10)
```

## Typical distribution

| Bus | Top driver | % of trips | Total drivers | Relief (<10 trips) |
|:---|:---:|---:|---:|---:|
| WGA422 | Cohen | 98% | 4 | 3 |
| WGD116 | Puello | 93% | 4 | 1 |
| WGB618 | Acosta | 90% | 10 | 8 |
| WGB039 | Mantilla | 80% | 19 | 15 |

## What to report

- **% by top driver**: shows stability. >90% = very stable. <60% = high rotation.
- **Relief count**: how many sporadic drivers appeared. The raw count is deceptive — most "drivers" are once-off relief.

## Correlation with lost trips

Buses with a single dominant driver (>90% of trips) tend to have:
- Lower lost-trip percentage
- Fewer "AUSENCIA CONDUCTOR" entries
- Better maintenance awareness (same driver reports issues consistently)

Buses with many relief drivers (>10) tend to have:
- Higher "AUSENCIA CONDUCTOR" losses
- More "TALLER" entries (relief drivers don't report mechanical issues early)
- Inflated unique driver count
