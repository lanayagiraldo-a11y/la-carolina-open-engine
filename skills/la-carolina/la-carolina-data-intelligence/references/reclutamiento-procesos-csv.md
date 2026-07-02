# Recruitment Process CSV — Schema

**Source:** SharePoint `LACAROLINABI / Procesos de reclutamiento.xlsx` → Sheet `PROCESOS DE CONTRA`
**Vault path:** `empresas/la-carolina/dashboard-redes-ads-reclutamiento/data/procesos_contratacion_raw.csv`
**Records:** 3,654

## Columns

| Column | Description | Example |
|---|---|---|
| `FECHA DE CREACION` | Date applicant entered funnel | 2026-01-08 |
| `NOMBRE` | Full name | HERNANDO REAL PADILLA |
| `CEDULA` | ID number | 72047607 |
| `CELULAR` | Phone | 3004269530 |
| `REINGRESO` | Is re-entry? | SI / NO |
| `ESTADO DEL PROCESO` | Current funnel stage | CONTRATADO / CIERRE DE PROCESO / CITADO |
| `CAUSA NO CONTRATO` | Why not hired | NO SE AJUSTA AL PERFIL / DESISTIMIENTO / INCOMUNICADO / EXAMENES MEDICOS |
| `OBSERVACION` | Free-text notes | "SE CITO EL 02 DE ENERO..." |
| `SIMIT` | Traffic debt status | OK / DEUDA |
| `VALOR` | Debt amount if SIMIT=DEUDA | 1027197 |
| `ANTECEDENTES POLICIA` | Police records | SI / NO |
| `RUNT` | License category | C1 / C2 |
| `MEDIO DE POSTULACION` | Recruitment source | WHATSAPP / COMPUTRABAJO / REFERIDO / VARYLO / MANYCHAT / REINGRESO / REINTEGRO / AFILIADO |
| `FECHA DE EX. MEDICOS` | Medical exam date | 2026-01-10 |
| `FECHA DE CITACIÓN` | Appointment date | 2026-01-02 |
| `FECHA DE CONTRATO` | Hiring date | 2026-01-24 |

## Known source values for `MEDIO DE POSTULACION`

| Source | Frequency | Notes |
|---|---|---|
| WHATSAPP | ~2,905 (80%) | Dominant channel but lower conversion |
| COMPUTRABAJO | ~313 (9%) | Job portal |
| REFERIDO | ~206 (6%) | Best conversion rate (51%) |
| VARYLO | ~66 (2%) | Recruitment platform |
| MANYCHAT | ~68 (2%) | Chatbot funnel |
| REINGRESO | varies | Returning driver |
| REINTEGRO | varies | Re-integration |
| AFILIADO | rare | Direct from afiliado |

## Conversion rates by source (from dashboard analysis)

| Source | Postulados | Contratados | Conversion |
|---|---|---|---|
| WhatsApp | 2,905 | 174 | 6% |
| Computrabajo | 313 | 12 | 3.8% |
| **Referido** | **206** | **105** | **51%** |
| ManyChat | 68 | — | — |
| Varylo | 66 | — | — |

## Top causes of non-hiring

1. No se ajusta al perfil: 1,493
2. Desistimiento: 1,164
3. Sin dato: 460
4. Exámenes médicos: 158
5. Incomunicado: 142

## Matching technique

For cross-referencing fleet drivers against this CSV:

```python
def normalize_name(n):
    n = n.upper().strip()
    n = re.sub(r'[^\w\s]', ' ', n)
    n = re.sub(r'\s+', ' ', n)
    return n.strip()

# Match requires >=2 significant (>2 char) words overlapping
rec_words = set(w for w in normalize_name(csv_name).split() if len(w) > 2)
driver_words = set(w for w in normalize_name(driver_name).split() if len(w) > 2)
match = len(rec_words & driver_words) >= 2
```
