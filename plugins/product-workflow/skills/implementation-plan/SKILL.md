---
name: implementation-plan
description: "Turn approved product scope, domain rules, journey specs, and solution design into a dependency-safe phased plan of small vertical tasks in docs/implementation-plan.md and .workflow/tasks.json. Use after solution design is settled, or when approved upstream changes add work to an existing plan. Does not write code."
metadata:
  version: "4.0"
---

# Implementation Plan

Read and apply the [shared workflow contract](../product-discovery/references/workflow-contract.md) before the skill-specific passes below. It defines cumulative decisions, completion evidence, minimal upstream correction and the planning/execution boundary. For direct filesystem/CLI operation only, use the [standalone host](../product-discovery/references/cli-host.md); managed hosts supply those mechanics.


## Entry gate

Require current `docs/product.md`, `docs/domain.md`, `docs/specs.md`, and `docs/architecture.md` grounded in settled owner decisions and completed upstream reasoning. No open technical unknown may remain if its answer could change task order or acceptance. If anything upstream is open, route it back instead of planning around it.

Do not implement code.

**Delta mode.** When approved upstream changes add work to an existing plan, plan only the new/affected tasks with new IDs (never renumber) and run the four checks on the changed tasks and their neighbours. Tasks may only depend on tasks above them in the file, so place new work carefully:
- **Release gate still open** — insert the new tasks above the gate and add the new leaf tasks to the gate's `deps`.
- **Release gate already signed off** — leave the old gate as history with `"releaseGate": false`, add the new tasks in a new phase at the end, and finish with a new gate task (`"releaseGate": true`) that depends on the old gate and the new leaves.

## Building the plan

1. List the **capabilities** later work depends on (app boots, backend reachable, identity exists, membership exists, the core record exists, …) and which journeys/flows consume each.
2. Pull forward any narrow technical investigation whose answer gates later work. An investigation answers one blocking question with evidence; keep throwaway exploration out of production code unless deliberately adopted.
3. Cut **tracer-bullet vertical slices**: a narrow but complete behavior through the layers it needs, verifiable on its own. Use horizontal/refactor tasks only when they genuinely gate slices; for wide mechanical migrations use expand → migrate → contract.
4. Among dependency-valid orders, prefer the one that proves the defining user loop earliest and retires the biggest integration/architecture risk earliest. Polish and engagement follow the core loop.
5. Group slices into phases, each with a one-line outcome.
6. Run the four checks below, then present the checkpoint.

Every task:
- delivers one concrete behavior or enabling prerequisite,
- is small enough for one fresh implementation context,
- has a precise one-line title and a one-line **Done when** that states observable acceptance,
- depends only on tasks that appear above it in the checklist,
- references the smallest relevant `J-xx` / `D-xx` / `ADR-xx` sources without copying them,
- avoids source-file paths unless the path itself is the decision.

## The four checks

These are mandatory. Each produces evidence for the checkpoint; a check is not "passed" by asserting it.

### 1. Source trace — no invented behavior

Every clause of every **Done when** that introduces user-visible behavior or consequential policy must trace to an approved `J-xx`, `D-xx`, or `ADR-xx`. Infrastructure tasks may exist without a journey (`refs: ["ARCH"]` or `["ENG"]`), but may not invent a user action, permission, setting, confirmation, visible failure policy, money/quantity rule, cardinality, or terminal-state rule because implementation seems to need one.

If a flow needs user-provided state that no spec defines — for example, a payout step that needs a recipient account nobody is ever asked to enter — stop and route the smallest missing behavior upstream. Do not plan a task to fill the hole.

### 2. Dependency closure — nothing needs the future

For each task, walk every noun/state its Done when consumes (identity, membership, record, device, external account, …) and name its **producer**: the task itself, an earlier declared blocker, or something already in the repository. Every capability must have exactly one producer.

The plan is invalid if any Done when needs a capability first produced by a later task — including implicitly. A task that says it does *not* implement auth/payments/notifications may not have a Done when that quietly requires them. Watch especially for a client feature scheduled before the server-authoritative rule it relies on, and for E2E/device verification required before that runtime or integration is configured.

Fix in this order of preference: rewrite the Done when to verify only what the task owns; move the prerequisite earlier; add the real prerequisite as an earlier task; merge only if the result still fits one fresh context. Never pull future behavior forward just to make an earlier Done when pass.

**Regression example — bootstrap before auth.**
- ✗ `TASK-01` Scaffold app and API — Done when: the client calls a **protected** API endpoint. `TASK-02` Add sign-in.
- Every blocker points backwards, yet TASK-01 is unverifiable: "protected" needs identity, which TASK-02 produces.
- ✓ TASK-01's Done when verifies an **unprotected** health endpoint; the protected call moves to TASK-02's Done when.

Then **dry-run** every initially ready task (one per parallel branch) and the first task of each phase: with only that task, its blockers, and its refs loaded, could a fresh implementer finish and verify the Done when (tests/build/device) without reading ahead or inventing a rule? If not, re-plan.

Hard phase check: if all later phases disappeared, could this phase be completed **and verified** using only the repository and earlier phases?

### 3. Minimal blockers and order

A blocker is valid only if removing it makes a specific Done when impossible. For each edge ask: *which exact acceptance clause would fail without it?* No answer → remove the edge. Earlier-in-the-same-journey is not a dependency.

Keep independent branches parallel. Typical false blockers:
- a direct-assignment path blocked on the self-service claim path of the same state machine,
- a first-user business task blocked on inviting a second user, unless it verifies multi-user behavior,
- an owner-side task blocked on a consumer-device task, unless it verifies the consumer view.

Split a task whose scenarios have different prerequisites when bundling would delay an independently verifiable scenario. Merge tasks only when they share blockers, trust boundary, and verification environment and still fit one fresh context. For a small product, more than ~2–3 active tasks per journey or ~25 overall is a signal to run one clustering pass.

When several valid orders remain, present the trade-off rather than pretending one is required. Never override an order the user explicitly approved.

### 4. Release closure

Exactly one task carries `releaseGate: true`. It must transitively depend on every other task, so it cannot become ready while any release branch is unfinished. Add only the minimal missing leaf blockers, not every ancestor. The validator enforces this mechanically.

## Artifacts

`docs/implementation-plan.md` — a phased checklist from `assets/implementation-plan-template.md`. Each task is exactly two lines:

```
- [ ] TASK-01 <precise title>
  Done when: <observable acceptance; may cite J-xx / D-xx>
```

No Scope/Verification paragraphs, capability tables, audit prose, HTML comments, or implementation diaries. Soft target: **<= 16 KB** for ~25–30 tasks.

`.workflow/tasks.json` — machine bookkeeping only, from `assets/tasks-template.json`:

```json
{"version": 1, "tasks": {"TASK-02": {"deps": ["TASK-01"], "refs": ["J-01", "ADR-01"]}}}
```

- `deps` — hard blockers only (check 3).
- `refs` — non-empty; each is `J-xx`, `D-xx`, `ADR-xx`, `ARCH`, or `ENG`. User-visible tasks include at least one `J-xx`.
- `releaseGate: true` on exactly one task.
- No titles or acceptance prose (the Markdown owns those). `gates` is temporary execution state owned by the execution workflow.

Sign-off is strict:
- unfinished: `- [ ] TASK-xx …`, optionally suffixed `— In progress`, `— Verification blocked`, or `— Changes required`;
- complete: `- [x] TASK-xx … ✅`, only after implementation verification, code review, security review (`PASS`/`NOT REQUIRED`), and E2E (`PASS`/`NOT APPLICABLE`). A completed task keeps its `Done when:` line.

Preserve task IDs; never renumber referenced tasks.

## Validation

Use the host's structural/reference/dependency validation. Direct CLI operation follows the standalone host reference; ai-os validates the six staged files. Explain warnings and report unavailable checks as pending, never passed. Structural validation does not prove source trace or capability producers: supply the concrete reasoning evidence below.

## Verification boundaries

Read the project's engineering policy. Plan focused E2E for completed features and meaningful user-visible edge cases, with applicable static/build checks. Do not add default unit/integration suites or TDD. For a genuine enabling prerequisite, name why feature E2E cannot usefully run yet and the downstream dependent feature that covers it. Present that boundary for explicit host/owner approval; never waive the release gate's E2E. Keep this proposal in handoff evidence/host metadata rather than adding unsupported keys to tasks.json or expanding the two-line task format.

## Phase-exit checkpoint

Present the whole plan as one reviewable unit — never task-by-task approval. Include:
- **Plan** — phases with outcomes; every task with its title and Done when,
- **Producer map** — a compact table (chat only, not persisted): capability → producing task → consuming tasks. Every task's `deps` entry must appear as at least one row, so a blocker with no row is unexplained,
- **Parallel branches** — which branches run independently after which task,
- **Dry run** — every initially ready task and the first task of each phase, **one line per task**, naming the capabilities its Done when consumes and which blocker produces each. Grouping tasks ("T03–T05 check out") is not evidence,
- **Source trace** — any Done when clause that needed an upstream fix, or `None`,
- **Investigations** — plan-gating investigations complete or scheduled first,
- **Validator output** — pass, plus warnings explained,
- **Decisions for the user** — only genuine choices: order trade-offs, granularity, deferrals,
- **Unresolved planning decisions** — must be `None` to exit.

When the four checks and upstream criteria have concrete evidence and no material gap remains, report planning complete. Present genuine order, granularity and verification-boundary choices together, reusing choices already accepted. The owner reviews the complete build plan once before separately authorizing execution (for example, Start factory); do not ask for task-by-task or ceremonial phase approval. Keep **Status:** Draft until an actual plan approval is supplied. Planning does not write code or start implementation; the execution host prepares fresh focused contexts.
