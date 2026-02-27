Analyze the following task and perform a thorough codebase impact research using parallel subagents.

**Task:** $ARGUMENTS

---

## Instructions

You are orchestrating a codebase impact analysis. Follow these steps:

### Step 1 — Parse the Task

Extract from the task:
- Core domain concepts (e.g., "authentication", "payments", "notifications")
- Technical artifacts mentioned (API endpoints, function names, DB tables, config keys)
- Type of change (feature, bug fix, refactor, performance, security)

### Step 2 — Identify Research Directions

Break the task into 2–5 independent research directions. Each direction should cover a distinct area of the codebase. Examples:
- Data layer (models, schemas, migrations, repositories)
- API / backend routes and controllers
- Business logic / services / domain layer
- Frontend components and UI flows
- Tests, mocks, and test utilities
- Configuration, environment variables, feature flags, infrastructure

List each direction before spawning agents, in this format:

```
Spawning subagents:
  [1] <Direction name> — <what it will search for>
  [2] <Direction name> — <what it will search for>
  ...
```

### Step 3 — Spawn Parallel Subagents

For each research direction, launch a **codebase-researcher** subagent using the Task tool with `subagent_type: "codebase-researcher"`. Run all subagents **in parallel** in a single message.

Each subagent prompt should be a self-contained ticket that includes:
- The original task for context
- Its specific research direction and what to focus on
- Instruction to return a structured impact report (Direct Changes, Coupled Code, Indirect Risk, Tests, Config/Infra)

### Step 4 — Synthesize Results

After all subagents complete, merge their reports into a single unified impact map. Deduplicate overlapping findings. Resolve any conflicts by keeping the most specific/detailed entry.

Output the final report in this format:

---

## Task Summary
<one-line summary of the task>

## Research Coverage
| # | Direction | Focus Area |
|---|-----------|------------|
| 1 | ... | ... |

## Unified Impact Map

### Direct Changes
- `path/to/file.cpp/hpp/h:L10-L40` — <reason>

### Coupled Code (Breakage Risk)
- `path/to/caller.cpp/hpp/h:L55` — <how coupled, what could break>

### Indirect / Regression Risk
- `path/to/related.cpp/hpp/h:L100` — <shared dependency or state>

### Tests to Update / Add
- `path/to/test.cpp/hpp/h:L200` — <scenario covered>

### Config / Infra / Migrations
- `path/to/config.yaml` — <what needs to change>

## Risk Summary
<3-6 sentences: overall risk level, most dangerous touch points, hidden coupling to watch out for>

## Recommended Investigation Order
1. <highest-risk file or area> — <why start here>
2. ...
