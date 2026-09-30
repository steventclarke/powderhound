# Ski Trip Planner: Product Spec & Agentic Workflow

> **How to use this file:** Part 1 describes the development workflow. Part 2 is the spec skeleton.
> In a real repo you'd split these (see "Suggested repo layout"). Anything marked `TODO` is yours to fill in.
> Hints are in *italics*. Delete them as you complete each section.

---

# Part 1: The Workflow

## 1.1 Core idea

In spec-driven development, **the spec is the source of truth and code is a derived artifact**. Instead of
prompting an agent with "build me a login page," you write down *what* and *why* precisely enough that an agent
(or a human) can implement it, verify it, and be corrected against it. Your job shifts from typing code to:

1. Deciding what to build (spec)
2. Constraining how it's built (plan, conventions)
3. Breaking work into verifiable pieces (tasks)
4. Reviewing results against the spec (verification)

The most important habit: **when the code and spec disagree, fix the spec first, then regenerate or adjust the code.**
If you only patch code, the spec rots and the workflow stops working.

## 1.2 The loop

```
 ┌─────────────┐   ┌───────────┐   ┌──────────┐   ┌───────┐   ┌───────────┐   ┌────────┐
 │ 0. Ground   │ → │ 1. Specify│ → │ 2. Clarify│ → │ 3. Plan│ → │ 4. Tasks  │ → │ 5. Build│
 │ rules/context│   │ (what/why)│   │ (Q&A)     │   │ (how)  │   │ (small)   │   │ + test  │
 └─────────────┘   └───────────┘   └──────────┘   └───────┘   └───────────┘   └────┬───┘
                          ▲                                                          │
                          │              ┌───────────────┐                           │
                          └──────────────│ 6. Verify &   │◄──────────────────────────┘
                            update spec  │ review        │
                                         └───────────────┘
```

### Phase 0: Ground rules (once, then maintain)
Create a persistent instruction file the agent reads every session (`AGENTS.md`, or `CLAUDE.md` for Claude Code).
Keep it short and concrete:
- Tech stack and versions, how to run tests/lint/build
- Code conventions and directory structure
- "Never do" rules (no secrets in code, no new dependencies without asking, don't edit generated files)
- Pointer to the spec: "Read `spec/` before starting any task"

### Phase 1: Specify (you, with agent as a sounding board)
Write the *what* and *why*: users, problems, user stories, acceptance criteria. **Do not specify tech choices here.**
Use the agent to challenge you: *"Read this spec and list ambiguities, missing edge cases, and contradictions."*

### Phase 2: Clarify
Have the agent interview you: *"Ask me up to 10 questions that would most reduce ambiguity in this spec. Don't
propose solutions yet."* Record answers back into the spec (or the Open Questions/Decision Log), not just in chat.
Chat history is ephemeral; the spec is not.

### Phase 3: Plan (technical design)
Now specify the *how*: architecture, stack, data model, API contract, integration approach. Ask the agent to
propose 2 to 3 options with trade-offs for the big decisions, then **you** choose and record the decision.
Output: a plan doc plus contracts (OpenAPI schema, DB schema) that are concrete and diffable.

### Phase 4: Tasks
Break the plan into small, ordered, independently verifiable tasks. A good task:
- Fits in one agent session (roughly one PR, ideally under a few hundred lines changed)
- References the spec requirement IDs it satisfies (e.g., `FR-012`, `US-03`)
- Has explicit "done when" criteria that can be checked by a test or command
- Lists files/areas expected to change and what must *not* change

### Phase 5: Build (agent implements, one task at a time)
Per task: fresh session or cleared context, point the agent at the spec + plan + task, and ask it to
**write tests first (or alongside), then implement, then run the tests**. Work on a branch per task. Commit often
so you can revert cheaply.

### Phase 6: Verify & review
- Run tests, lint, type-check yourself. Don't trust "all tests pass" claims without seeing it.
- Review the diff against the *acceptance criteria*, not just for code quality.
- Optional: a second agent session acts as reviewer with a prompt like *"Review this diff against
  `FR-012` and `FR-013`. List requirements not met, behavior not covered by tests, and scope creep."*
- Update spec if reality taught you something. Log it in the Decision Log.

## 1.3 Practices that matter most for agentic work

| Practice | Why |
|---|---|
| **Small tasks** | Agent quality degrades on large, vague tasks; small ones are reviewable and revertable. |
| **Acceptance criteria as tests** | Gives the agent a way to check itself and gives you a fast pass/fail signal. |
| **Contracts first** (OpenAPI, DB schema, TypeScript types) | Frontend, backend, and integration work can proceed in parallel against a fixed interface. |
| **Fake/mock providers early** | Build against recorded fixtures of weather/flight/lodging responses so development doesn't depend on live APIs, rate limits, or cost. |
| **Human checkpoints** | You approve spec → plan → tasks before implementation starts. Cheap to fix early, expensive later. |
| **Fresh context per task** | Long sessions accumulate stale assumptions. Let the spec files carry the context. |
| **Agent proposes, you decide** | Especially for architecture, data model, security, and anything hard to reverse. |
| **Traceability** | Requirement IDs in tasks, commits, and tests let you answer "is FR-012 actually done?" |

## 1.4 Common failure modes

- **Spec too vague:** the agent fills gaps with plausible-but-wrong assumptions. Fix: acceptance criteria + examples.
- **Spec too prescriptive too early:** you've written pseudo-code; nothing is gained. Keep the spec at behavior level.
- **Skipping the plan:** agents will pick a stack and structure for you, inconsistently across sessions.
- **Big-bang implementation:** "build the whole app" produces something that looks done and isn't.
- **Spec drift:** code evolves, spec doesn't. Make "update spec" part of the definition of done.
- **Trusting integrations blindly:** agents may invent API endpoints or parameters. Always give them the
  provider's real docs (paste or link into the repo under `docs/providers/`) and verify against a live sandbox call.

## 1.5 Suggested repo layout

```
/
├── AGENTS.md                  # (or CLAUDE.md) persistent agent instructions
├── spec/
│   ├── 00-overview.md         # vision, goals, non-goals, glossary
│   ├── 01-requirements.md     # user stories, functional + non-functional reqs
│   ├── 02-domain-model.md     # scoring logic, entities
│   ├── 03-integrations.md     # provider evaluation, mapping, fixtures
│   ├── 04-architecture.md     # the plan: stack, components, decisions
│   ├── 05-api.openapi.yaml    # backend contract
│   ├── decisions/             # ADR-001-*.md, one per significant decision
│   └── tasks/                 # T-001-*.md, one per task, with status
├── docs/providers/            # saved third-party API docs and sample responses
├── frontend/
├── backend/
└── fixtures/                  # recorded provider responses for tests
```

## 1.6 Suggested order of work for *this* project

Front-load risk. The riskiest parts are not the CRUD and login; they're **data availability and the scoring model**.

1. **Spike / feasibility (before finalizing the plan):** confirm which weather and travel data providers you can
   actually use, at what cost, with what terms of service, rate limits, and access requirements. Save sample
   responses. Update `03-integrations.md`. *Some travel-data APIs have restrictive access, and this may change your scope.*
2. **Define "good snowfall chance" precisely** (see Domain Model section). This is the product's core value, and it's
   a modeling question as much as an engineering one.
3. **Walking skeleton:** thinnest end-to-end slice, e.g. hardcoded resort list → one weather provider → one
   scoring function → API endpoint → minimal UI table. No login, no travel data yet.
4. **Add travel cost** for one provider behind an adapter interface; introduce combined ranking.
5. **Auth and user accounts, saved searches, alerts.**
6. **Hardening:** caching, rate limiting, error states, observability, deployment.

## 1.7 Tooling options (pick one to start; the workflow is tool-agnostic)

- **Plain markdown + a coding agent** (e.g., Claude Code, Cursor, Copilot agent mode): most flexible, lowest overhead. Recommended for your first project.
- **Spec-driven toolkits** (e.g., GitHub Spec Kit, AWS Kiro): bake in specify → plan → tasks stages and templates. Worth trying once you've felt the manual version.
- Whatever you pick, check current docs; this space moves fast.

## 1.8 Example prompts for each phase

- **Specify review:** *"Read `spec/00-overview.md` and `01-requirements.md`. List ambiguities, missing edge cases, untestable requirements, and conflicts. Don't rewrite anything."*
- **Clarify:** *"Ask me the 10 highest-value clarifying questions about this spec, one at a time."*
- **Plan:** *"Given the spec, propose two architecture options for the backend and integrations layer with trade-offs. Wait for me to choose."*
- **Tasks:** *"Break the plan into tasks of roughly one PR each. For each: goal, requirement IDs, files touched, done-when criteria, dependencies. Order by dependency."*
- **Build:** *"Implement `spec/tasks/T-004-weather-adapter.md`. Read the spec and plan first. Write failing tests, implement, run them. Stay in scope. Report anything ambiguous instead of guessing."*
- **Verify:** *"Compare the diff on this branch to requirements FR-010 through FR-014. Table: requirement, met/partial/not met, evidence."*

---
