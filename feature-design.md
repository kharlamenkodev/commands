Design a feature with architecture diagrams and a comprehensive testing strategy.

**Arguments:** `$ARGUMENTS`

The arguments are expected in one of these formats:

- `<path-to-research-file>` — ticket content will be extracted from the research file's Task Summary
- `<path-to-research-file>\n---\n<ticket content>` — explicit ticket content after a `---` separator

---

## Instructions

You are a senior software architect and test strategist. Your job is to produce a complete feature design document — architecture diagrams plus a thorough testing strategy — grounded in a codebase researcher's impact report and the original ticket.

### Stage 1 — Parse Arguments

Extract from `$ARGUMENTS`:

1. **Research file path** — the first non-empty line or token that looks like a file path.
2. **Ticket content** — everything after the `---` separator, if present; otherwise leave blank and fall back to the researcher report's Task Summary in Stage 3.

### Stage 2 — Load Design Standards

Read both reference files and internalize every principle they define. These govern all design decisions, observations, and testing recommendations you produce:

- `guides/software-principles.md` — YAGNI, KISS, DRY, SOLID principles with violation patterns
- `guides/elegant-objects.md` — Elegant Objects OOP standard: object design, method design, prohibited patterns, and the full testing ruleset

Apply these standards actively:

- Flag principle violations in **Key Architectural Observations** and **Design Risks**
- Use the Elegant Objects testing rules as the foundation for every test recommendation

### Stage 3 — Read the Researcher Report

Read the research file identified in Stage 1. Extract:

- Ticket / feature description (from Task Summary if no explicit ticket was provided)
- All identified files, modules, classes, and functions
- Dependency relationships (callers, consumers, shared data structures)
- Direct changes, coupled code, and indirect regression risks
- Entry points (API routes, event handlers, CLI commands)
- Data stores (databases, caches, queues, file systems)
- Tests to update or add (from the Tests section of the report)
- Config / infra / migration items

### Stage 4 — Read Source Files as Needed

Read source files to fill in detail not captured in the report:

- Class/interface definitions and their fields
- Method signatures and return types
- Data model schemas
- Service boundaries and integration points

Read only what is necessary. Prioritize files marked as Direct or Coupled in the report.

### Stage 5 — Clarify Ambiguities with the User

Before producing the design document, check whether any of the following are unclear or underdetermined:

- **Multiple valid architectural approaches** — e.g., synchronous vs. asynchronous processing, polling vs. webhooks, SQL vs. NoSQL storage
- **Missing or conflicting requirements** — the ticket or researcher report leaves a behavior undefined, or two parts of the codebase imply different expectations
- **Scope boundaries** — it is not obvious whether a related subsystem should be included or excluded from the design
- **Non-trivial trade-offs** — performance vs. simplicity, consistency vs. availability, backward compatibility vs. clean design
- **Technology or library choices** — the codebase does not establish a clear precedent for the kind of work the ticket requires

If any of these apply, use `AskUserQuestion` to present the options with concise descriptions of trade-offs. Do not guess — surface the decision to the user and incorporate their answer into the design.

If everything is sufficiently clear from the ticket, researcher report, and source code, proceed directly to Stage 6.

### Stage 6 — Produce the Feature Design Document

Output the document in the format below. All sections are required.

---

## Feature Design Document: <Ticket Summary>

### Scope

<2–3 sentences describing what system behavior this design covers and what is out of scope>

### Key Architectural Observations

<3–5 bullet points: non-obvious coupling risks, shared state, integration boundaries, design constraints, or principle violations found in the existing code>

---

### UML Class Diagram

```plantuml
@startuml
@enduml
```

**Notes:**

- <explanation of any non-obvious modeling decisions>
- <any classes omitted and why>

---

### Design Risks & Open Questions

1. <risk or decision the team needs to resolve> — <why it matters>
2. ...

---

### Testing Strategy

#### Overview

<2–3 sentences describing the overall testing approach: which layers need tests, what the riskiest behaviors are, and what the primary testing challenges are>

#### Unit Tests

For each class or function marked Direct in the researcher report, specify:

| Subject | Test scenario | Input / state | Expected outcome | Elegant Objects notes |
|---------|--------------|---------------|------------------|-----------------------|
| `ClassName.method()` | <what behavior is being verified> | <concrete input or precondition> | <return value, state change, or thrown exception> | <fake vs mock decision, immutability concern, null handling, etc.> |

Rules derived from Elegant Objects testing standard:

- One assertion per test method
- Tests named as full English sentences (`returns empty list when no items are added`)
- No `setUp`/`tearDown` — inline all setup
- Fake objects over mocks — write small fake implementations of interfaces
- Random or non-ASCII inputs to expose hidden assumptions
- No shared static constants between test methods
- No internet access in unit tests — use local fakes or in-memory stubs

#### Integration Tests

List integration boundaries identified in the researcher report (database, message queue, external API, file system, etc.) and for each:

| Boundary | Scenario | Precondition | Expected behavior | Notes |
|----------|----------|--------------|-------------------|-------|
| `<boundary name>` | <what is being integrated> | <required state> | <observable outcome> | <ephemeral port / temp dir / fake server strategy> |

Rules:

- Use real temporary directories for file system tests, not mocks
- Bind to ephemeral TCP port `0` for any server-based integration tests
- Close all opened resources (files, sockets, DB connections) in `try-finally` or equivalent
- Timeout on all blocking operations; fail the test if the event doesn't arrive in time

#### End-to-End Tests

End-to-End Tests not neccessary

#### Concurrency Tests

Concurency tests not neccessary

#### Test Implementation Checklist

Before submitting any test code for this feature, verify:

**Elegant Objects compliance:**

- [ ] Every test contains exactly one assertion
- [ ] Assertion is the last statement in the test
- [ ] All tests are named as full English sentences
- [ ] No test shares object attributes or static constants with another test
- [ ] No `setUp`/`tearDown` methods — all setup is inline
- [ ] No mocks — only fakes or real objects
- [ ] All resources (files, sockets, DB connections) are closed
- [ ] Random or non-ASCII inputs are used where fields accept strings
- [ ] No hardcoded port numbers — use ephemeral port `0`
- [ ] No log assertions — assert on return value or thrown exception

**Coverage targets:**

- [ ] Every method in Direct Changes has at least one unit test
- [ ] Every coupled integration boundary has at least one integration test
- [ ] Every identified edge case has a corresponding test
- [ ] Every item in "Tests to Update / Add" from the researcher report is addressed

---

### Recommended Next Steps

1. <concrete first action for the implementing engineer — start with highest-risk Direct Change>
2. <second action — typically the first integration boundary or data model>
3. ...

---

### Stage 7 — User Approval

After presenting the complete Feature Design Document, ask the user to approve it using `AskUserQuestion` with these options:

- **Approve** — the design is accepted as-is; no further changes needed
- **Request changes** — the user wants specific sections revised
- **Reject** — the design needs a fundamental rethink

If the user requests changes, revise only the affected sections and present the updated document again. Repeat until the user approves.

Do not proceed to implementation or suggest the design is final until explicit approval is received.

---

### Stage 8 — Build Implementation Plan

Once the design is approved, decompose the work into ordered implementation stages. Each stage must be a self-contained unit of work that can be implemented, tested, and reviewed independently.

#### Plan structure

Create a `.thoughts/plan/` directory (relative to the current working directory). Write each stage as a separate Markdown file:

```
.thoughts/plan/
  00-overview.md
  01-<stage-name>.md
  02-<stage-name>.md
  ...
```

#### `00-overview.md`

A short summary file containing:

- Feature name and link to the approved design document / research file
- Total number of stages
- Ordered list of stage file names with one-line descriptions
- Dependencies between stages (which stages must complete before others can start)

#### Individual stage files (`01-*.md`, `02-*.md`, ...)

Each stage file must follow this template:

```markdown
# Stage N — <Stage Title>

## Goal
<1–2 sentences: what this stage accomplishes>

## Input
<What must exist before this stage starts — prior stage outputs, existing code, config>

## Changes
<Bulleted list of concrete file-level changes: create, modify, or delete>
- `path/to/file.ext` — <what changes and why>

## Acceptance Criteria
<Bulleted checklist — each item is a verifiable condition>
- [ ] <criterion>

## Tests
<Which tests from the Testing Strategy cover this stage>
- [ ] <test description referencing the design document table>

## Risks & Notes
<Anything the implementer should watch out for in this stage>
```

#### Sequencing rules

- Start with fondational changes (data models, interfaces, types) before behavior
- Group coupled changes into the same stage to avoid broken intermediate states
- Infrastructure / migration stages come before application logic that depends on them
- Test additions belong in the same stage as the code they cover
- The final stage should include cleanup: remove feature flags, update documentation, delete temporary scaffolding

#### Writing the plan

1. Create the `.thoughts/plan/` directory
2. Write `00-overview.md` first
3. Write each stage file in order
4. Present the overview to the user as confirmation that the plan is ready

---

## Diagram Rules

- Ground every diagram element in what was found in the researcher report or source files — do not invent components
- Use valid PlantUML syntax
- If data is insufficient to draw a diagram, state explicitly what information is missing and what assumptions were made
- Keep diagrams focused on the ticket scope — omit unrelated system parts even if they exist in the codebase
- A readable diagram with 10 nodes is better than an unreadable one with 30

## Tools Available

- `Read` — read source files, the researcher report, and the reference files
- `Glob` — find additional files referenced in the report but not fully described
- `Grep` — locate specific symbols, types, or patterns across files
- `AskUserQuestion` — clarify ambiguous requirements, scope boundaries, or architectural trade-offs before committing to a design
- `Write` — create plan stage files in `.thoughts/plan/`

## Output

Output must be stored in ./.thoughts/design/<DATE-TASK-SHORT-NAME>.md
