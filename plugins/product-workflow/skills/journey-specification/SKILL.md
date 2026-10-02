---
name: journey-specification
description: "Turn approved product journeys and domain decisions into living behavioral specifications. Use after product discovery/domain modeling, before technical design or implementation. Synthesize settled behavior rather than reopening product discovery."
metadata:
  version: "2.0"
---

# Journey Specification

## Entry gate

Require approved/current product scope and a domain-modeling checkpoint with no unresolved business rule needed by the release journeys. If a required rule is still open, route it to product discovery/domain modeling instead of encoding a guess in a spec.

Synthesize what has already been decided into focused, living journey sections in `docs/specs.md`.

**Delta mode.** For a change to an existing product, edit or add only the affected `J-xx` sections, then re-run the reference checks. Unaffected sections stay untouched.

Do not run a broad interview here. If a material behavior or business rule is genuinely unresolved, surface the exact ambiguity and return to product discovery or domain modeling instead of guessing.

If several independent ambiguities are discovered while specifying journeys, batch them into one upstream decision set rather than interrupting the user once per spec. Identify which journey each decision affects.

Create one focused `## J-xx — …` section per journey in `docs/specs.md` when the journey needs behavioral detail. The `J-xx` heading itself is the stable address; do not add HTML anchors or hidden comments. Downstream tools resolve IDs from headings, so the document stays plain Markdown. A spec should describe observable behavior through the product's public/user-facing seams:
- actor and outcome,
- important preconditions,
- successful behavior,
- meaningful failure/edge behavior,
- relevant domain rules/invariants by reference,
- explicit out-of-scope behavior when needed to prevent accidental expansion.

Prefer examples and concrete scenarios over implementation instructions. Do not specify frameworks, source file paths, database shapes, or internal class/function structure.

### Stable domain-reference rule

When a spec relies on a core domain invariant, reference its stable `D-xx` ID. Do not use positional shorthand such as `domain 12-18`, line ranges, or copied invariant text. Reference only the smallest relevant invariant set; lifecycle-specific behavior may point to the named state-model section instead of manufacturing another `D-xx`.

At entry and before exit, resolve every `D-xx` used by `docs/specs.md` against the current `docs/domain.md`. If domain modeling migrated/clusters invariant IDs, update the affected spec references in the same change. A stale domain reference is a blocking traceability defect even when the prose still sounds correct.

## Behavioral coverage pass

Before declaring the specs complete, build a lightweight internal traceability view:
- each approved journey -> its observable success/failure behavior,
- each user-visible domain rule/state transition -> at least one journey scenario that demonstrates its effect,
- each authorization boundary -> an observable allow/deny case where material,
- each intentional out-of-scope behavior -> explicitly excluded where omission could otherwise invite accidental expansion.

Do not copy every invariant into every spec. Reference domain truth and specify only the observable consequence. If a domain rule has no user-visible effect, it does not need a synthetic E2E-style scenario just to satisfy the matrix.

### Journey order

Present spec sections in the same order as the journeys in `docs/product.md`; product discovery owns that order. If a spec reveals that a journey consumes state produced by a later journey, route the ordering problem back to product discovery. Never renumber approved `J-xx` IDs.

### Cross-journey consistency pass

Walk a few chained scenarios where one journey produces state consumed by another. Check that actor identity, state names, time boundaries, monetary/quantity effects, and terminal outcomes remain consistent across specs. Examples should be chosen from the actual product rather than a fixed checklist.

If a chained scenario exposes an unresolved business rule, stop and route the exact gap upstream; do not make the specs mutually consistent by inventing behavior.

Use `assets/journey-spec-section-template.md` as a guide. Keep each journey section compact and independently addressable so an implementation agent can load or retrieve only that section without needing unrelated journeys.

Each journey section should normally contain only a one-line outcome/contract, concise scenario/outcome bullets, relevant domain-rule references, and material exclusions. Do not copy the full invariant text, product rationale, or architecture constraints into the spec. Prefer compact declarative scenarios over verbose Given/When/Then prose when both express the same observable contract. Soft target for the consolidated file: **<= 16 KB** for roughly ten journeys; exceed deliberately only when behavior truly requires it.

When intended behavior changes later, update the authoritative spec before or with the implementation change; do not create a competing version.

## Phase-exit checkpoint

Before moving to solution design, present a compact checkpoint containing:
- **Journeys specified** — IDs/titles and artifact paths,
- **Behavioral coverage** — happy paths plus meaningful failure/edge behavior represented; list any user-visible domain rule or state transition with no demonstrating scenario (should be `None`), and the chained scenarios walked in the cross-journey pass,
- **Upstream references** — all referenced `D-xx` IDs resolve to the current domain model; no positional/stale domain references remain,
- **Validation** — output of `python3 tools/validate-workflow.py` from the project root; must pass, warnings explained. If the tool is missing, run the project setup from product-discovery (files in `../product-discovery/assets/project-setup/`, relative to this skill's directory) first; never skip validation,
- **Unresolved behavior** — must be `None` to exit; otherwise name the upstream phase that owns it,
- **Exit criteria** — state whether every approved journey needed for the planned release now has an observable behavioral contract.

Do not ask for line-by-line approval of every spec. Ask for one phase approval only if the specification phase introduced or changed consequential behavior; otherwise report completion and proceed according to the workflow.
