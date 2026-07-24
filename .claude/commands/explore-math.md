---
description: Turn a theorem/statement/proof into N competing interactive visual explainers, built by subagents and judged by a reviewer
argument-hint: "<theorem / statement / proof to explain>"  [N=5]
allowed-tools: Task, Bash, Read, Write, Edit, Glob, Grep, WebFetch
---

Follow the procedure in @explore_math_explanations.md **exactly** for the statement below.

**Statement to explain:** $ARGUMENTS

Defaults (override only if the argument specifies otherwise):
- **N = 5** competing concepts.
- **Delivery** = standalone offline dirs under `./<topic>/<theorem>/<idx>/` (open by double-click, no build step, no network at view time).
- **Audience** = motivated student who has seen the basics of the field.

Execute the phases in order:
1. **Phase 0** — understand the statement from first principles (produce the `piece | why it's hard | candidate visual metaphor` table).
2. **Phase 1** — generate N distinct general concepts with zero-padded indexed slugs; run the coverage check.
3. **Phase 2** — ⏸️ present the concept table + assumptions (topic/theorem slugs, N, audience, delivery) and **STOP for my explicit approval**. Do not spin up any subagents until I approve.
4. **Phase 3** — after approval, launch `general-purpose` subagents **one at a time**, telling each what the prior ones built so it stays distinct.
5. **Phase 4** — run one reviewer subagent that opens and judges every artifact; loop weak concepts back to Phase 3 with a sharper brief and more budget.
6. **Phase 5** — present the comparison table and offer next steps.

Honor every hard-won lesson in the procedure file (offline `file://` constraints, vendor all libs locally, compute don't fake, surface caveats, diversify stacks, never trust a builder's self-report).
