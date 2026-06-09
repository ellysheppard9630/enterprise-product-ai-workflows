---
name: engineering-handoff-prep
description: prepare a feature, project, or spec for engineering handoff using dev-planning-lead-product-manager, dev-planning-sr-developer, dev-execution-api-developer, dev-execution-frontend-developer, and dev-execution-task-coordinator. use when requirements are mostly defined and the goal is to turn them into a clearer, build-ready handoff with better implementation detail, frontend and backend alignment, and practical task decomposition.
---

Use this skill to convert a mostly-defined feature or project into a cleaner, more implementation-ready engineering handoff.

This skill is for late planning and pre-delivery shaping. It is especially useful when the product direction is mostly settled, but the handoff still needs sharper implementation detail, better frontend and backend alignment, and a more practical execution breakdown before engineering starts.

Follow this workflow in order.

## Workflow overview

1. Understand and normalize the handoff input
2. Review the handoff through a PM lens
3. Stress-test implementation practicality
4. Evaluate backend and API needs
5. Evaluate frontend and UX behavior needs
6. Break the work into execution-ready tasks
7. Synthesize the final handoff package

## Step 1: Understand and normalize the handoff input

Accept flexible input. The user may provide:
- a PRD
- a feature spec
- a handoff draft
- a project summary
- requirements notes
- workflow descriptions
- acceptance criteria
- stakeholder requirements
- a pasted document fragment

First, normalize the material into a working summary with:
- problem being solved
- target user or customer
- proposed capability
- intended outcome
- primary workflows
- known constraints
- dependencies already mentioned
- obvious implementation assumptions
- areas that are still unclear

If the input is incomplete or rough, proceed anyway. Do not block unless there is too little information to identify the intended feature or change. If needed, make the best possible interpretation and explicitly label assumptions.

## Step 2: Review through a PM lens

Use `dev-planning-lead-product-manager` to evaluate whether the handoff communicates the right product intent.

Focus on:
- whether the problem and user value are clear
- whether the scope is coherent
- whether the handoff distinguishes requirements from assumptions
- whether the feature intent is likely to be interpreted consistently by engineers
- whether non-scope and tradeoffs are clear enough
- whether important business constraints are visible

This step should ensure the handoff still reflects the right product shape before it becomes implementation detail.

## Step 3: Stress-test implementation practicality

Use `dev-planning-sr-developer` to evaluate whether the work is buildable as described.

Focus on:
- missing implementation details
- hidden complexity
- sequencing issues
- state and workflow ambiguity
- data dependencies
- edge cases
- areas where different engineers might make different assumptions
- what needs to be clarified to reduce build risk

This step should expose the practical execution issues that often cause churn after kickoff.

## Step 4: Evaluate backend and API needs

Use `dev-execution-api-developer` to review the backend implications of the handoff.

Focus on:
- API and service needs
- request and response expectations
- validation rules
- permissions and auth considerations
- error-handling expectations
- contract assumptions
- backend dependencies
- missing technical detail that could affect implementation

If the proposal has little or no meaningful backend or API component, keep this section concise and say so clearly.

## Step 5: Evaluate frontend and UX behavior needs

Use `dev-execution-frontend-developer` to review the frontend implications of the handoff.

Focus on:
- component and screen-level behavior
- user flows and interaction patterns
- loading, empty, success, and error states
- validation behavior
- state transitions
- accessibility concerns
- frontend/backend contract expectations
- places where UI behavior is too vague for implementation

If the proposal has little or no meaningful frontend component, keep this section concise and say so clearly.

## Step 6: Break the work into execution-ready tasks

Use `dev-execution-task-coordinator` to convert the clarified handoff into a practical work breakdown.

Focus on:
- major workstreams
- dependency order
- critical path items
- parallelizable work
- hidden tasks
- integration or QA coordination needs
- recommended breakdown into implementation-ready chunks

This step should turn the handoff into something teams can plan and execute more confidently.

## Step 7: Synthesize the final handoff package

Synthesize all findings into a refined engineering handoff.

Explicitly identify:
- what is already clear and build-ready
- what still needs clarification
- where frontend and backend assumptions may diverge
- what should be resolved before engineering starts
- what can safely be resolved during implementation
- how the work should be broken down for smoother execution

Do not merely stack agent outputs together. Resolve overlaps, highlight conflicts, and produce one practical, PM-usable handoff improvement package.

## Output format

Use this exact section structure unless the user asks for something different:

# Engineering Handoff Prep

## 1. Handoff summary
- problem
- target user or customer
- proposed capability
- intended outcome
- primary workflows
- known constraints
- dependencies
- key assumptions

## 2. PM alignment review
Summarize the most important findings from `dev-planning-lead-product-manager`.

## 3. Implementation feasibility review
Summarize the most important findings from `dev-planning-sr-developer`.

## 4. Backend and API review
Summarize the most important findings from `dev-execution-api-developer`.

## 5. Frontend and UX behavior review
Summarize the most important findings from `dev-execution-frontend-developer`.

## 6. Task breakdown and sequencing
Summarize the most important findings from `dev-execution-task-coordinator`.

## 7. Cross-functional gaps
Identify the most important mismatches, ambiguities, or missing details across product, frontend, backend, and execution planning.

## 8. Build-ready handoff recommendations
Provide the specific clarifications, additions, or revisions needed to improve the handoff.

## 9. Suggested work breakdown
Provide a practical best-effort breakdown of:
- major workstreams
- dependency order
- critical path items
- parallelizable work
- coordination needs

## 10. Recommended next step
Recommend the most appropriate next action, such as:
- proceed to engineering handoff
- clarify implementation details
- define frontend/backend contracts
- tighten scope
- add missing workflow states
- break work into smaller delivery units

## Synthesis rules

- Do not assume a good PRD is automatically a good engineering handoff.
- Do not ignore frontend or backend ambiguity just because product intent is clear.
- Distinguish implementation blockers from ordinary build-time details.
- Make assumptions explicit.
- Highlight where teams could interpret the same requirement differently.
- Optimize for a handoff that reduces churn, rework, and conflicting assumptions.
- Keep the output practical for a technical PM preparing work for engineers.
- If the handoff is already strong, say so clearly, but still identify any residual risk.

## Good use cases

Use this skill for:
- preparing a feature for engineering
- improving a handoff draft
- aligning frontend and backend expectations
- clarifying implementation detail before sprint planning
- shaping complex work before story breakdown
- reducing ambiguity before engineering kickoff

## Not ideal for

This skill is less useful for:
- raw discovery notes
- early brainstorming
- high-level architecture exploration
- final code review
- deep integration verification
- post-implementation QA validation

Use other skills for those workflows.