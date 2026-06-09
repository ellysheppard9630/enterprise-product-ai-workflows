# Calculation Model — Roadmap Estimate

Reproduces the team's "Proposal Log and Estimate Template" math exactly — the **same model `funded-estimate` uses**, by design (the PO chose to mirror the billable math). The skill computes this itself and never writes into the `.xlsx`.

The whole model hangs off **one estimated input per Feature — construction/dev hours**. Everything else derives from it.

**Run the model per Feature, then roll up.** A roadmap project (Epic) has multiple Features. Estimate each in-scope Feature's construction hours + confidence, run this model **per Feature**, then **sum the per-Feature bucket totals and costs to the project total**. Per-Feature runs keep a low-confidence Feature carrying its own (heavier) padding instead of letting one project-wide confidence wash it out.

---

## What the cost figure means (read this — it's the honest caveat)

This uses PDI's **standard blended delivery rate (~$272/hr, never above $275)** — the same rate `funded-estimate` puts in front of customers. That is the rate PDI **charges/values delivery at**, *not* raw loaded internal labor cost. So the dollar figure this skill produces is **"delivery value / planned delivery spend at standard rate,"** consistent with how funded work is valued — it is **not** a finance-grade internal-cost-of-labor number.

Always label the output that way. If leadership specifically needs cost-of-labor (a smaller number), that's a different rate the PO would supply — flag it, don't silently relabel this figure as "what it costs us."

**No customer, no discount.** This is an internal roadmap forecast. The discount mechanic from `funded-estimate` does **not** apply — there is no customer to discount. Omit it.

---

## Constants (defaults — overridable per run)

**Bucket derivation, as % of construction:**

| Bucket | % of construction |
|---|---|
| Requirements, Analysis & Design | 30% |
| Construction (the estimated input) | 100% |
| Testing | 33.33% |
| Tech Writers | 13% |

**Padding distribution** (how the total padding pool is spread across buckets — sums to 100%):

| Bucket | Padding share |
|---|---|
| Requirements, Analysis & Design | 15% |
| Construction | 55% |
| Testing | 25% |
| Tech Writers | 5% |

**Rates:**

| Bucket | Rate |
|---|---|
| Tech Writers | **$225/hr** |
| Everything else (Req/Analysis/Design, Construction, Testing, Refinement, SQL) | **$275/hr** |

**Optional raw add-ons** (entered directly, **no padding applied**): Refinement, SQL/automated testing.

**QA branch scaling** (multi-version testing): when QA must validate a Feature across multiple Enterprise versions/branches, the Testing bucket scales. Let `B` = number of branches (from Step 1; default 1). Branch count can differ per Feature — apply each Feature's own `B`.

`branch_factor = 1 + (B − 1) × 0.5` — first branch full; each additional branch ≈ half the base QA (test design is reused, but execution, branch-specific setup, regression, and defect re-verification repeat). Applied to the **Testing bucket only**.

| Branches B | branch_factor |
|---|---|
| 1 | 1.0 |
| 2 | 1.5 |
| 3 | 2.0 |
| 4 | 2.5 |

(The 0.5 per-branch factor is a starting default — revisit if it proves inaccurate in practice.)

---

## Formulas (per Feature)

Let `K` = a Feature's construction hours (confirmed in Step 4), `C` = that Feature's confidence weight (0–1).

**1. Before-padding bucket hours**
```
req   = K × 0.30
const = K
test  = K × 0.3333 × branch_factor          # branch_factor = 1 + (B−1)×0.5 ; B=1 ⇒ ×1.0
tw    = K × 0.13
subtotal = req + const + test + tw          # = K × 1.7633 when B=1; testing term grows with B
```

**2. Padding pool (confidence-weighted)**
```
padding_total = round( subtotal − (subtotal × C) )   # = subtotal × (1 − C)
```
Lower confidence → larger padding pool.

**3. Distribute padding**
```
pad_req   = round( padding_total × 0.15 )
pad_const = round( padding_total × 0.55 )
pad_test  = round( padding_total × 0.25 )
pad_tw    = round( padding_total × 0.05 )
```

**4. Total hours per bucket** (= before-padding + its padding share)
```
hrs_req   = round(req)   + pad_req
hrs_const = round(const) + pad_const
hrs_test  = round(test)  + pad_test
hrs_tw    = round(tw)    + pad_tw
hrs_refine = <raw, if any>     # no padding
hrs_sql    = <raw, if any>     # no padding
feature_hours = hrs_req + hrs_const + hrs_test + hrs_tw + hrs_refine + hrs_sql
```

**5. Cost per bucket** (each bucket at its own rate)
```
cost_req    = hrs_req    × 275
cost_const  = hrs_const  × 275
cost_test   = hrs_test   × 275
cost_tw     = hrs_tw     × 225          # tech writers
cost_refine = hrs_refine × 275
cost_sql    = hrs_sql    × 275
feature_cost = sum of the above
```

**6. Project roll-up** (across all in-scope Features)
```
project_hours = Σ feature_hours
project_cost  = Σ feature_cost
```
No discount step. The two project totals — **project_hours** and **project_cost** — are the headline forecast.

---

## Quick cross-check (sanity, not a substitute)

Per Feature:
```
feature_hours (before raw add-ons) ≈ K × 1.7633 × (2 − C)
```

| Confidence C | Multiplier vs. construction |
|---|---|
| 0.3 | ~3.0× |
| 0.5 | ~2.6× |
| 0.7 | ~2.3× |
| 0.9 | ~1.9× |

If a computed Feature total is far from `construction × multiplier`, something is off. (The 1.7633 assumes B=1; with multiple QA branches the testing term becomes `0.3333 × branch_factor`, so the multiplier rises accordingly.)

**Blended rate** lands ~$272/hr for a typical mix (tech writers are a small, lower-rate slice). It is **never above $275** and rarely below ~$268. Check it at **both** levels: each Feature *and* the project roll-up (`project_cost ÷ project_hours`). The reconciliation guardrail (SKILL Step 6) uses this.

---

## Worked example (one Feature, matches the template exactly)

Construction `K = 222`, confidence `C = 0.30`, single QA branch:

| Bucket | Before padding | + Padding | Total hrs | Rate | Cost |
|---|---|---|---|---|---|
| Requirements/Analysis/Design | 67 (222×.30) | 41 | 108 | $275 | $29,700 |
| Construction | 222 | 151 | 373 | $275 | $102,575 |
| Testing | 74 (222×.3333) | 69 | 143 | $275 | $39,325 |
| Tech Writers | 29 (222×.13) | 14 | 43 | $225 | $9,675 |
| Refinement (raw add-on) | — | — | 80 | $275 | $22,000 |
| SQL testing (raw add-on) | — | — | 0 | $275 | $0 |
| **Feature totals** | subtotal 391 | pad 275 | **747** | | **$203,275** |

- Padding pool = 391 × (1 − 0.30) = **274** (≈275 after per-bucket rounding).
- Blended rate = 203,275 ÷ 747 = **$272.1/hr**. ✓ (in band)
- This is **one Feature**. A roadmap project sums many of these. If this Epic had three Features of similar size, the project forecast would be on the order of ~2,240 hrs / ~$610K — that rolled-up pair (project hours, project cost) is what leadership sees, labeled as planned delivery spend at standard rate.
