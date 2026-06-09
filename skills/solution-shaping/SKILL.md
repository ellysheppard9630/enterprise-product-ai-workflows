---
name: solution-shaping
description: shape and compare product or technical solution options using dev-planning-lead-product-manager, dev-planning-lead-architect, dev-planning-sr-architect, dev-planning-sr-developer, dev-planning-devex-engineer, and lead-qa-engineer. use when a repo, module, workflow, feature idea, or technical proposal needs to be researched before implementation, especially when Claude should inspect the context, generate multiple viable options, debate tradeoffs, rank options from easier to harder delivery, surface open questions and blockers, and prepare implementation-ready or leadership-review documentation.
---

Use this skill to shape the right solution before detailed implementation planning begins.

This skill is for the stage after the problem is mostly understood but before the solution has fully solidified. It is especially useful when multiple solution directions are possible, when tradeoffs are unclear, or when a proposal needs to be shaped into something both valuable and realistically buildable.

Follow this workflow in order.

## Workflow overview

1. Understand and normalize the problem and proposed direction
2. Frame the product need and solution intent
3. Evaluate high-level solution options and boundaries
4. Pressure-test the detailed technical shape
5. Evaluate implementation practicality
6. Evaluate developer workflow and delivery friction
7. Evaluate quality strategy and testability
8. Synthesize the recommended solution direction

## Step 1: Understand and normalize the problem and proposed direction

Accept flexible input. The user may provide:
- a problem statement
- a feature or project concept
- a rough spec
- stakeholder requests
- a current-state workflow description
- one or more possible solution ideas
- architecture notes
- a pasted document fragment

First, normalize the material into a working summary with:
- problem being solved
- target user or customer
- current pain points
- desired outcome
- proposed solution ideas already mentioned
- constraints already known
- dependencies already known
- obvious assumptions
- areas that are still unclear

If the input is incomplete, messy, or contains multiple directions, proceed anyway. Do not block unless there is too little information to identify the problem space. If needed, make the best possible interpretation and explicitly label assumptions.

## Step 2: Frame the product need and solution intent

Use `dev-planning-lead-product-manager` to clarify the product framing.

Focus on:
- the actual problem to solve
- the user or customer value at stake
- whether the proposed solution is targeting the right need
- which outcomes matter most
- what should be in scope versus out of scope
- where the current framing is too broad, too narrow, or too solution-led
- what product tradeoffs are implied

This step should anchor the solution around the right problem and business value before deeper technical shaping begins.

## Step 3: Evaluate high-level solution options and boundaries

Use `dev-planning-lead-architect` to shape the high-level solution direction.

Focus on:
- major solution options
- recommended system boundaries
- component responsibilities
- tradeoffs between approaches
- complexity versus flexibility
- dependency implications
- where the solution may be over-engineered or under-defined
- what architectural decisions need to be made early

This step should identify the most promising solution shape at a high level and explain why.

## Step 4: Pressure-test the detailed technical shape

Use `dev-planning-sr-architect` to evaluate the stronger candidate solution more deeply.

Focus on:
- interface assumptions
- workflow and data movement
- non-functional requirements
- security, reliability, performance, and operability concerns
- technical design gaps
- risks that could emerge if engineering starts with insufficient clarity
- whether the detailed shape is coherent enough to move toward implementation planning

This step should expose whether the proposed solution is genuinely sound, not just directionally appealing.

## Step 5: Evaluate implementation practicality

Use `dev-planning-sr-developer` to evaluate whether the solution is practical to build.

Focus on:
- hidden complexity
- sequencing issues
- implementation ambiguity
- data and state-management concerns
- risky dependencies
- edge cases that materially affect the solution choice
- where engineers are likely to diverge in interpretation
- what should be clarified to reduce churn during execution

This step should ensure the chosen solution direction is realistic for actual delivery.

## Step 6: Evaluate developer workflow and delivery friction

Use `dev-planning-devex-engineer` to assess how the solution will affect the engineering experience.

Focus on:
- developer setup and tooling burden
- testability and debugging ergonomics
- workflow friction
- CI/CD or environment concerns
- observability and supportability needs
- opportunities for simplification, automation, or better conventions
- whether the proposed solution would create unnecessary developer pain during build and maintenance

This step should help shape a solution that is not only technically valid but also sustainable for the team to implement and operate.

## Step 7: Evaluate quality strategy and testability

Use `lead-qa-engineer` to assess how the solution can be validated and where quality risk concentrates.

Focus on:
- how testable the proposed solution is as shaped
- the test strategy and coverage approach across unit, integration, and end-to-end
- release confidence and rollout risk
- where defects or regressions are most likely to concentrate
- data, environment, and dependency needs for testing
- observability needed to validate behavior in production
- whether quality or testability concerns should reshape the solution before implementation

This step should ensure the recommended solution can be validated with confidence, not just built.

## Step 8: Synthesize the final solution direction

Synthesize all findings into a recommended solution direction.

Explicitly identify:
- the best-fit solution approach
- why it is preferred over alternatives
- the most important tradeoffs
- the key scope and boundary decisions
- what should be decided now versus later
- the biggest risks
- what needs further clarification before engineering handoff
- what can safely remain flexible during later planning

Do not merely stack reviews together. Resolve overlap, surface tensions, and produce one practical, coherent recommendation.

## Output format

Use this exact section structure unless the user asks for something different:

# Solution Shaping

## 1. Problem and solution summary
- problem
- target user or customer
- desired outcome
- proposed solution ideas
- known constraints
- dependencies
- key assumptions

## 2. Product framing
Summarize the most important findings from `dev-planning-lead-product-manager`.

## 3. High-level architecture direction
Summarize the most important findings from `dev-planning-lead-architect`.

## 4. Detailed technical shape review
Summarize the most important findings from `dev-planning-sr-architect`.

## 5. Implementation practicality review
Summarize the most important findings from `dev-planning-sr-developer`.

## 6. Developer experience and delivery friction review
Summarize the most important findings from `dev-planning-devex-engineer`.

## 7. Quality and testability review
Summarize the most important findings from `lead-qa-engineer`.

## 8. Recommended solution direction
Describe the preferred solution shape, including:
- core approach
- major boundaries and responsibilities
- why this direction is preferred
- what tradeoffs are being accepted

## 9. Alternatives considered
List the most relevant alternative approaches and why they were not chosen as the primary recommendation.

## 10. Key risks and open decisions
List the most important unresolved issues, dependencies, and decisions still needing attention.

## 11. Recommended next step
Recommend the most appropriate next action, such as:
- draft the spec
- define system boundaries more clearly
- narrow scope
- validate a technical assumption
- prepare for engineering handoff
- split the solution into phases

## Synthesis rules

- Do not let a proposed solution dictate the problem framing prematurely.
- Do not optimize for elegance at the expense of practicality.
- Do not over-engineer for speculative future needs.
- Make tradeoffs explicit.
- Distinguish high-confidence recommendations from still-open design choices.
- Keep the output practical for a technical PM and engineering leads shaping work before implementation.
- Favor a clear, buildable direction over an exhaustive catalog of options.
- If the input points to the wrong solution for the stated problem, say so clearly.
- If multiple solution paths remain viable, recommend one while naming the conditions that could justify another.

## Good use cases

Use this skill for:
- shaping solution direction after discovery
- comparing possible implementation approaches
- clarifying boundaries before spec writing
- choosing between simpler and more flexible designs
- aligning product and engineering on solution shape
- evaluating technical tradeoffs before handoff

## Not ideal for

This skill is less useful for:
- raw discovery notes with no proposed direction
- final engineering handoff prep
- detailed API contract design
- deep integration validation
- sprint task decomposition
- final code review

Use other skills for those workflows.