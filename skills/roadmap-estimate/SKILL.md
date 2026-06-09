---
name: roadmap-estimate
description: "Show the business how much time and money a roadmap project is slated to consume — a repo-grounded, leadership-facing forecast of planned delivery spend, built the same way as funded-estimate but pointed at a roadmap project instead of a single funded customer ask. Accepts an ADO roadmap Epic (pulls its child Features via the ADO MCP) OR a pasted project description. Does full repo-grounded construction estimation on every in-scope Feature, runs each through enterprise software company's confidence-padding model (the exact same $275/~$272-blended math funded-estimate uses), rolls the Features up into a single project total, surfaces the planned calendar window, and outputs a forecast for leadership plus a per-Feature engineering appendix. No customer, no SOW, no discount — internal planning only, read-only. Triggers on: roadmap estimate, roadmap spend, how much will this roadmap project cost, what are we slated to spend, planned spend for this epic, cost of a roadmap epic, time and money for this project, estimate a roadmap project, roadmap investment, roadmap forecast, what's this epic going to cost us, size this roadmap work."
---

# Roadmap Estimate (Leadership-Facing Spend Forecast, Repo-Grounded)

Help a enterprise software company Product Owner / PM show the business **how much time and money a roadmap project is slated to consume** — grounded in the actual codebase, rolled up across the project's Features, and framed for leadership. This is the planning-side mirror of `funded-estimate`: same repo-grounding discipline, same padding math, but the unit of work is a **roadmap project (an ADO Epic and its Features)**, not a single funded customer ask, and the deliverable is an **internal investment forecast**, not a customer price or SOW.

**The problem this solves:** leadership wants a defensible "what is this roadmap project going to cost us, and over what window" before committing capacity — and getting that today means waiting on manual EM sizing across every Feature. This skill repo-grounds the construction hours per Feature and rolls them into a project forecast in minutes, with a human signing off on the construction breakdown before any dollar figure appears.

## Where this fits

`roadmap planning → **roadmap-estimate (this skill — planned hours + spend across the project's Features)** → capacity/portfolio decision → (if a Feature later becomes funded customer work, that's funded-estimate's job)`

- **Upstream:** the project usually already exists as a `Roadmap=True` Epic with child Features in ADO. If it doesn't yet, a pasted description works too.
- **Sibling:** `roadmap-checker` finds and lists Roadmap Epics/Features. This skill *estimates* the spend on one. If you need to first find the Epic, use `roadmap-checker`; then bring the Epic ID here.
- **Downstream:** the per-Feature detail goes to engineering to verify; the project totals feed a portfolio/capacity conversation. This skill stops at the forecast. It does **not** write a SOW, a plan, or anything customer-facing.

## What this skill IS

- A way to self-serve a **leadership-facing forecast of planned roadmap spend**: point at an Epic (or describe the project), get back construction hours grounded in real code for each Feature, run through the team's padding model, rolled up to one project total (hours + dollars) with the planned calendar window.
- A **speed play.** Minutes, not weeks of cross-Feature EM sizing. The skill does the load-bearing analysis; a human reviews the construction hours before any dollar figure is finalized.

## What this skill is NOT

- **Not funded-estimate.** That sizes a single *funded customer ask* and ends in an engineering change-list + SOW inputs and a customer price. This sizes a *roadmap project we're planning to build ourselves* and ends in an internal forecast. No customer, no SOW, no discount.
- **Not a finance-grade cost-of-labor number.** It uses the standard blended delivery rate (~$272/hr) — what enterprise software company *values/charges* delivery at, the same rate funded-estimate shows. That is **not** raw internal loaded labor cost (which is lower). Label it as planned delivery spend at standard rate. See `references/calculation-model.md`.
- **Not roadmap-checker.** That finds what's on the roadmap. This prices one item that's already on it.
- **Not a prioritization or staffing decision.** It surfaces the number; humans decide what to do with it.
- **Not production code, and not a writer to ADO.** Read-only investigation only — never edits, comments on, or transitions work items, and never writes into the team's `.xlsx`.
- **Not a number that ships unreviewed.** Construction hours are generated, then the PO confirms/adjusts and engineering verifies the per-Feature detail before the forecast is treated as committed.

---

## Core rules

- **Repo-grounded, always. No vibe numbers.** Every construction hour must trace to specific, named surface area in the actual codebase — files, screens, stored procs, tables, jobs, integrations. If you can't ground it, say so; that uncertainty lowers confidence and raises padding. Cite real paths (`Repos/<name>/<path>`).
- **Set the forecast basis up front: total vs. remaining.** Establish in Step 1 whether the forecast is **total project value** (estimate as if greenfield — every component costed as if built from scratch) or **remaining spend to finish** (cost only the un-built work). The two diverge enormously for an in-flight project, and getting it wrong wastes the whole run. **Then verify against the repo:** while grounding (Step 3), tag each component **built / partial / not-built**; if the code contradicts the stated basis (e.g. the PO said greenfield but the Epic is 90% already built), stop and re-confirm the basis before estimating — never silently estimate on the wrong basis. When the PO is unsure, ground first, report how much already exists, then let them choose.
- **Estimate every in-scope Feature, then roll up.** The project total is the **sum of per-Feature model runs**, not one estimate of the whole Epic. Run the full calculation model per Feature (so a shaky Feature carries its own heavier padding) and sum the bucket totals + costs to the project forecast.
- **Scope the path of least resistance, per Feature.** For each Feature, estimate the simplest *product-way* change that genuinely satisfies its intent — not the most complete or elegant build. Heavier approaches are surfaced as **explicit optional add-ons** on that Feature, never folded into the headline. Least-resistance must still actually deliver the Feature — if the cheap path risks not covering it, flag it.
- **Construction hours are generated, then human-reviewed before any dollars.** Show the per-Feature construction breakdown and get the PO's explicit confirm/adjust **first**. Only after sign-off do you compute and reveal cost. Never jump to a dollar figure.
- **Same math as funded-estimate.** Use `references/calculation-model.md` verbatim — $275/hr ($225 tech writers), confidence-weighted padding, QA branch scaling. The only differences: run it per Feature and roll up, and **drop the discount** (no customer).
- **Confidence drives padding, honestly — per Feature.** Self-assess `C` for each Feature from how cleanly it mapped to real code. A thin or net-new Feature gets *lower* confidence and *more* padding. Never inflate confidence to tighten the project number.
- **The cost figure is delivery spend at standard rate — say so.** It is consistent with funded estimates, not a cost-of-labor figure. If leadership specifically wants loaded internal labor cost, that's a different rate the PO must supply; flag it rather than relabeling.
- **Skip work that's already done.** Features in `Closed`/`Done` (or `Removed`) state are shipped (or dropped) — don't forecast spend on them. List them separately as "already delivered / not counted" so the project total reflects only *remaining* planned work. Call out the distinction.
- **Surface the planned window, but keep it distinct from effort.** Roadmap items carry `StartDate`/`TargetDate`. Report the planned **calendar span** alongside the **effort hours** — they answer different questions ("by when" vs. "how much work") and must never be conflated.
- **Run the reconciliation guardrail before finalizing.** Implied rate (`cost ÷ hours`) must land ~$268–$275 at **both** the Feature and project level. If it doesn't, **raise an alert and stop** — see Step 6. This catches construction-only hours shown next to a full cost.
- **The agent pressure-test is opt-in and off by default.** After the construction breakdown is drafted, *offer* it; run a **targeted** senior-engineering panel only if the PO accepts, and only on the riskiest/largest Features. The PO still owns the final figure at the Step 4 gate. Never dispatch the full roster.
- **Internal only — never send anything externally.** This produces a forecast for the PO and leadership. It does not email anyone or call any Microsoft 365 / Outlook tool, and it does not draft a SOW.
- **ADO is read-only.** Pull Epics/Features through the ADO MCP only. Never create, edit, comment on, or transition work items.
- **Respect the PO's override.** If the PO adjusts a Feature's construction hours, confidence, branch count, or which Features are in scope, accept it — they know the plan and may know things the repo doesn't.

---

## The calculation model

Full formulas, constants, the per-Feature-then-roll-up procedure, and a worked example are in `references/calculation-model.md` — **read it before computing.** Summary:

- **Only construction/dev hours are estimated directly, per Feature.** Everything else derives from it.
- **Derived buckets, as % of construction:** Requirements/Analysis/Design = 30%, Testing = 33.33%, Tech Writers = 13%, Construction = 100% (the estimated input).
- **QA branch scaling:** Testing bucket × `1 + (branches − 1) × 0.5`, applied per Feature (a Feature tested across Enterprise 15.9 + 16.1 = 2 branches = ×1.5) before padding.
- **Confidence weight `C` (0–1) → padding:** `padding hours = subtotal × (1 − C)`, per Feature.
- **Rates:** $275/hr everything except Tech Writers $225/hr; blended lands ~$272/hr.
- **Roll-up:** sum per-Feature bucket hours and costs to `project_hours` / `project_cost`. **No discount** (internal forecast).

---

## Step 1 — Gather inputs

Ask in order, one at a time. Don't batch into a checklist.

| # | Question | Required? | Notes |
|---|---|---|---|
| 1 | Roadmap project as an **ADO Epic ID**, or a **pasted description**? | Required | Epic → Step 2 pulls its Features. Description → skip Step 2, you define the Feature/work breakdown with the PO in Step 3. |
| 2 | **Forecast basis: total project value, or remaining spend to finish?** | Required | **Total** = estimate as if greenfield (whole build costed fresh) — for total investment/value sizing. **Remaining** = cost only the un-built work — for "what are we slated to spend to land it." If the PO is unsure, choose **auto-detect**: ground the repo first (Step 3), report how much is already built, then have them pick. In-flight projects usually want *remaining*; genuinely-new ones want *total*. This choice changes how Step 3 counts built components — set it before grounding. |
| 3 | Which enterprise software company repo(s) does the project touch? | Required | See repo list below. Multiple is fine; different Features may touch different repos. |
| 4 | How many branches/versions will QA cover? | Required | Default 1. Can vary per Feature (e.g., Enterprise 15.9.01 + 16.1 = 2; CK up to 4). Scales each Feature's Testing bucket. |
| 5 | Any extra context — PRDs, Feature descriptions, prior discovery docs? | Strongly preferred | Grounds scope per Feature. ADO Feature descriptions count; richer is better. |
| 6 | Confirm which Features are **in scope** for this forecast? | Required (after Step 2) | Default: all non-closed child Features. PO can trim. Closed/Removed are excluded and reported separately. |
| 7 | Project / initiative name (for the forecast header)? | Optional | Defaults to the Epic title. |
| 8 | Anything explicitly out of scope to call out? | Optional | Becomes the "Not included" section. |
| 9 | Does leadership want loaded internal labor cost instead of standard delivery rate? | Optional | Default = standard rate (mirrors funded-estimate). If yes, get the internal cost/hr from the PO and note both can't be the same number. |


## Step 2 — Pull the roadmap Epic + its Features from work tracking system (if an Epic ID was given)

All work tracking system access goes through  MCP — read-only. (For field reference-name quirks like `Custom.Roadmap`, see the `roadmap-checker` skill.)

1. **Get the Epic.** `mcp__ado__wit_get_work_item` for the Epic ID with `expand: relations` (or `$expand=All`). Capture: Title, State, AreaPath, `Microsoft.VSTS.Scheduling.StartDate`, `Microsoft.VSTS.Scheduling.TargetDate`, `Custom.InitiativeName`, `Custom.ProductArea`, Description.
2. **Get child Features.** Filter the Epic's relations for `System.LinkTypes.Hierarchy-Forward`, collect target IDs, and `mcp__ado__wit_get_work_items_batch_by_ids` for them (or a `queryType: "tree"` WIQL on `WorkItemLinks` — whichever the MCP supports cleanly). For each Feature capture: ID, Title, State, Description, `TargetDate`, AreaPath.
3. **Split by state.** In-scope = Features **not** in `Closed`/`Done`/`Removed`. Closed/Done = already delivered → list separately, **not** counted in the forecast. Removed = dropped → ignore.
4. **Confirm the in-scope Feature list with the PO** before estimating (Step 1 Q5). This is the spine of the whole forecast — get explicit agreement on what's being priced.

If no Epic was given (pasted description), skip this step: work with the PO to break the project into a Feature-level (or workstream-level) list to estimate against.

## Step 3 — Repo-grounded construction estimate, per Feature (the core)

This is the part that can't be done by hand quickly, and the part the forecast rests on. Stay read-only. **Do this for every in-scope Feature**, using the same investigation rigor as `funded-discovery`/`funded-estimate`:

**Apply the forecast basis (Step 1 Q2) as you ground.** For each Feature, tag every component **built / partial / not-built** against the real code (e.g. the gateway builder exists; the DB table doesn't; the connector delivery path is absent). Then:
- **Remaining basis** → cost only the *not-built* and *partial* components.
- **Total basis** → cost *all* components as if greenfield, using the existing code as the spec for what a full build entails (count built components as if they needed building).
- **Auto-detect** → compute the built-vs-remaining split, then **report it and have the PO choose the basis at the Step 4 gate before finalizing hours.**

If what you find contradicts the stated basis — e.g. the PO said greenfield but the Feature is essentially done, or said remaining but it's barely started — **surface it now and re-confirm; don't quietly proceed on the wrong basis.** Whichever basis is chosen, keep the built/not-built tags so you can show *both* the total and the remaining figure if asked.

**Scope the least-resistance, product-way solution first** for the Feature: the smallest configurable/multi-tenant-safe change that genuinely delivers its intent. Capture heavier approaches as explicit optional add-ons on that Feature, never folded into its baseline. If the cheap path risks not delivering the Feature, flag it.

For each Feature:
1. **Find existing precedent first.** Has enterprise software company already solved a near-identical problem in the repo? Clone-precedent work is far cheaper than net-new — the single biggest driver of hours and confidence.
2. **Map the surface area.** `ls`, `Glob`, `Grep` (and the `Explore` agent for broad fan-out, with explicit paths on the big repos — broad Explore agents fail on Enterprise/EnterpriseCloud). For SQL-heavy products lean on `tsqllsp` and `tsqlschema` to find affected procs, tables, and call chains.
3. **Trace the change across applications and sub-modules.** Real changes span a UI app + a service/job + the database + often an integration. `Enterprise` alone spans ~49 sub-modules. Name every impacted area — missed surface area is the #1 cause of a low estimate.
4. **Check cross-cutting plug-in points** that add forgotten hours: SIAI audit framework, cache-version plumbing, POS download / PBDX, SSRS report pairs (EN/fr), FocalPoint, multi-tenant config/resx.
5. **Decompose the baseline into discrete construction tasks**, each with an **hours range (low–high)** grounded in net-new vs. extend vs. clone-precedent, surface area, and integration/test complexity. Keep heavier approaches in a separate add-ons list.
6. **Sum the Feature's baseline tasks to a construction figure** (low–high; midpoint is the working input, movable in Step 4). Sum each add-on separately.
7. **Self-assess the Feature's confidence `C` (0–1):**
   - High (0.7–0.9): clear precedent, contained surface, well-understood Feature.
   - Medium (0.5–0.7): some net-new, spans a few apps, a couple unknowns.
   - Low (0.3–0.5): largely net-new, spans many apps/integrations, real ambiguity (common for thinly-described roadmap Features).
   Say *why* you picked the level. Thin Feature descriptions legitimately mean lower confidence — that's protective, not a failure.

Do not invent repo facts. If you can't find where something lives, that's a finding that lowers that Feature's confidence.

> **Scale note:** full repo-grounding *every* Feature is deliberate (the PO chose precision) but heavy on a large Epic. If the in-scope list is big (say 8+ Features), tell the PO it'll take a while and offer to estimate in batches, confirming each batch at the Step 4 gate as you go rather than one giant pass.

### Optional — agent pressure-test (ask the PO; off by default)

Once the construction breakdowns are drafted, **offer** it — never run automatically:

> *"Want me to pressure-test the riskiest Features' construction hours with a panel of senior-engineering agents before you review? It tightens the number on complex Features but adds time. (Worth it for low-confidence or M+-looking Features.)"*

If accepted, dispatch a **targeted** panel against the *largest/lowest-confidence* Features' task breakdowns — not the full roster, not every Feature — running independent passes in parallel:
- **`dev-planning-sr-developer`** + **`dev-planning-sr-architect`** — challenge the task list: missed surface area, hidden complexity, optimistic ranges.
- **`research-db-analyst`** — validate impacted procs/tables/schema (the hours driver on SQL-heavy products).
- **`dev-execution-integration-verifier`** — only if a Feature touches integrations/vendors.
- **`research-qa-analyst`** — sanity-check the testing assumption.

Fold findings back into the affected Features' construction hours + confidence, then show the PO what changed. The panel **refines**; the human still confirms at Step 4.

## Step 3.5 — Roadmap hygiene flags

Quick pass before the gate — surface (don't decide) anything that distorts the forecast:
- **Mid-flight on a total basis** → if much of the project is already built but the basis is *total*, flag plainly that the headline is **notional total value, not cash-to-finish**, and give the rough *remaining* figure alongside so leadership isn't misled.
- **Already-delivered Features** wrongly left in scope (Closed/Done) → move to the not-counted list.
- **Thinly-specified Features** that forced low confidence → flag so leadership knows the number is soft there and may need refinement before commitment.
- **Features that look mis-sized vs. their TargetDate** (e.g., a 600-hour Feature with a target two weeks out) → flag the schedule/effort mismatch; it's a planning signal.
- **Features that appear to overlap each other** (double-counted surface area) → flag so the same work isn't priced twice.

These are planning signals for the PO/leadership, each with a one-line reason. The skill flags; it doesn't re-plan the roadmap.

## Step 4 — Human review of the construction estimate (gate)

Show the PO the construction breakdown **before any dollars**:
- **The forecast basis** (total vs. remaining). If the project is mid-flight, show the **built-vs-remaining split** and *both* candidate figures so the PO costs the right thing. If basis was **auto-detect**, this is where they choose — and don't proceed until they do.
- Per-Feature construction hours ranges with repo evidence, plus each Feature's confidence and the one-line reason.
- Any optional add-ons listed separately per Feature (opt-in, not in the headline).
- The in-scope Feature set (and the excluded/already-delivered list).
- The summed project construction range.

Ask: *"Basis is **[total/remaining]**. Confirm the in-scope Features and their construction estimates / confidence — adjust any, switch basis, drop or add a Feature, or pull in any optional add-ons?"* Wait for explicit confirm/adjust. This gate is what keeps a generated number from quietly becoming a committed roadmap spend figure — and what keeps a *notional total* from being mistaken for *cash-to-finish*.

## Step 5 — Run the model + roll up

With each Feature's confirmed construction hours, confidence, and branch count, compute per `references/calculation-model.md`:
- Per Feature: derived buckets (Testing scaled by that Feature's branch factor), padding pool + distribution, per-bucket total hours, per-bucket cost at the right rate ($275 / $225 tech writers), → `feature_hours` and `feature_cost`.
- **Roll up:** `project_hours = Σ feature_hours`, `project_cost = Σ feature_cost`. No discount.
- Capture the planned calendar window: earliest Feature/Epic StartDate → latest TargetDate.

## Step 6 — Reconciliation guardrail + alerts

Before finalizing, run these and **raise any that trip**:
- **Rate reconciliation (hard stop), both levels.** `implied rate = cost ÷ hours` must land **~$268–$275** (never above $275) for **each Feature** and for the **project roll-up**. If outside the band — e.g. construction-only hours paired with a full cost — **stop and alert** and fix it.
- **Low-confidence alert.** Any Feature with `C ≤ 0.4`, or a project-weighted confidence that low: flag that the forecast carries heavy padding and real uncertainty; recommend refining those Features before treating the number as committed.
- **Large-project alert.** If `project_cost` is large (≥ ~$500K), or any Feature touches core posting logic, schema migration of existing data, vendor-protocol changes, or systemic refactor: flag that those Features warrant a full EM-led estimate beyond this PO-run forecast.
- **Thin-grounding alert.** If a named repo was empty/stub, or a Feature couldn't be mapped to real code, say so plainly per Feature — don't paper over it.
- **Counted-the-wrong-thing alert.** If a Closed/Done Feature slipped into the totals, or scope overlaps double-counted work, stop and correct before presenting.

Resolve or explicitly acknowledge every tripped alert with the PO before Step 7.

## Step 7 — Produce outputs

Produce these, in this order — only after the Step 4 gate and Step 6 checks:

1. **Roadmap spend forecast — the leadership headline (the deliverable).** Lead with the two project numbers and the window, then the per-Feature table. Shape:

```
Roadmap Spend Forecast — <Project / Epic title>  (Epic <id>)

Basis:              <TOTAL project value (notional, as-if-greenfield) | REMAINING spend to finish>
Slated to consume:  ~<project_hours> hrs   |   ~$<project_cost>  (planned delivery spend at standard blended rate ~$272/hr)
Planned window:     <StartDate> → <TargetDate>   (calendar span — separate from effort hours)
Scope:              <N> Features counted; <M> already delivered / not counted
                    <if mid-flight + total basis: "Note: ~X% already built; remaining-to-finish ≈ <hrs>/<$>">  

| Feature | ID | Est. hours | Est. spend | Confidence | Target |
|---------|----|-----------|------------|------------|--------|
| <title> | <id> | 747 | $203,275 | 0.30 (low — net-new, spans 3 apps) | 5/1/2026 |
| <title> | <id> | … | … | … | … |
| **Project total** | | **<sum>** | **$<sum>** | | |

Not counted (already delivered): <Feature> (<id>, Closed), …
```
   Plainly label the dollar figure as **delivery spend at standard rate**, not cost-of-labor.

2. **Per-Feature engineering appendix — clearly marked internal/supporting.** For each in-scope Feature, the granular construction change list (the funded-estimate shape: schema alters, named-screen UI changes, single procs, config keys — **dev hours bold** on meaningful lines, repo paths on leaf changes, a `Total dev: X hrs (lo–hi)` line per Feature), plus that Feature's bucket table (Req/Construction/Testing/Tech Writers + padding) and `feature_hours`/`feature_cost`. This is what engineering verifies.

3. **Roadmap hygiene flags (if any), from Step 3.5.** Short, clearly-labeled list — each flag with its one-line reason — so the PO/leadership knows what to refine or watch before committing capacity.

## Step 8 — Hand off

Tell the PO exactly what's next:

> *"The **Roadmap Spend Forecast** at the top is the leadership view — planned hours and delivery spend across the project's Features, plus the calendar window. The per-Feature appendix below it is for engineering to verify the construction work and hours. Reminder: this is a planning forecast at standard delivery rate, not a cost-of-labor figure and not a SOW — and the low-confidence Features flagged above should be firmed up before the number is treated as committed."*

Do not draft a SOW or plan. Do not write to ADO or the `.xlsx`. Do not email anyone. Do not save files unless the PO asks.

---

## Anti-patterns (don't do these)

- **Don't estimate on the wrong basis, or let a total-rebuild figure pass as cash-to-finish.** Set total-vs-remaining in Step 1, verify it against the repo while grounding, and re-confirm if the code contradicts it. For a mid-flight project on a *total* basis, always show the remaining figure too — a notional total mistaken for remaining spend is the most expensive error this skill can make.
- **Don't estimate the Epic as one blob.** The project total is the sum of per-Feature model runs — that's how a shaky Feature gets its own padding and how the per-Feature appendix stays honest.
- **Don't produce construction numbers that aren't grounded in the repo.** No surface area, no hours. Ungrounded guessing is the failure mode this skill replaces.
- **Don't relabel the rate as cost-of-labor.** It's standard delivery rate (what funded work is valued at), higher than loaded internal cost. Say so. Only show a true internal-cost number if the PO supplies that rate.
- **Don't count already-delivered (Closed/Done) Features in the spend total.** Forecast remaining planned work; list the delivered ones separately.
- **Don't conflate effort hours with the calendar window.** "How much work" and "by when" are different axes — report both, keep them distinct.
- **Don't gold-plate a Feature.** Smallest product-way change that delivers its intent is the baseline; heavier approaches are opt-in add-ons. (Flip side: don't under-scope into a number that can't deliver the Feature.)
- **Don't skip the human-review gate.** A generated construction figure never becomes a committed roadmap spend without the PO confirming the in-scope Features and their estimates.
- **Don't run the agent panel unless the PO opted in, and never the full roster or every Feature.** Targeted senior-engineering subset, on the riskiest Features only. The human still confirms at Step 4.
- **Don't inflate confidence to tighten the number.** Thinly-described Features → lower confidence → more padding, by design.
- **Don't ignore a tripped reconciliation alert** at either the Feature or project level — a mismatch means a number is wrong.
- **Don't write to ADO, write the `.xlsx`, draft a SOW, or send anything externally.** Read, compute, present, hand off.
- **Don't stay inside one repo if a Feature clearly spans more** — missed apps mean a low estimate.
- **Don't fabricate repo facts.** Cite real paths. "I couldn't find it" is a finding that lowers confidence, not something to hide.
