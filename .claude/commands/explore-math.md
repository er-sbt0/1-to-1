---
description: Turn a theorem/statement/proof into N competing interactive visual explainers, built by subagents and judged by a reviewer
argument-hint: "<theorem / statement / proof to explain>"  [N=5]
allowed-tools: Task, Bash, Read, Write, Edit, Glob, Grep, WebFetch
---

Follow the procedure in @explore_math_explanations.md **exactly** for the statement below. Use its defaults (N=5, standalone offline dirs under `./<topic>/<theorem>/<idx>/`, motivated-student audience) unless the argument overrides them. Run the entire procedure end to end **without stopping for approval** — do not pause at any phase; launch the subagents automatically once the concepts are generated.

**Statement to explain:** $ARGUMENTS
