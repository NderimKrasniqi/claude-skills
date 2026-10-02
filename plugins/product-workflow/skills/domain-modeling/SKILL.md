---
name: domain-modeling
description: "Sharpen and maintain the product's domain language and business rules after scope is understood. Use when terms, invariants, state transitions, or domain boundaries are unclear or changing."
metadata:
  version: "2.0"
---

# Domain Modeling

## Entry gate

Require an approved/current product scope whose discovery checkpoint has no unresolved product decision that would force domain modeling to invent user-visible policy. If the scope is not closed, return the exact unresolved items to `product-discovery` before modeling them as invariants.

Actively sharpen the domain model; do not turn it into architecture documentation.

Use the approved product journeys as context. Discuss concrete scenarios to expose ambiguity and edge cases. When the user uses vague or overloaded language, propose a precise canonical term. When an existing repo disagrees with the intended domain model, surface the contradiction instead of silently choosing one.

**Delta mode.** For a change to an existing product, touch only the terms, invariants, and state models the change affects. If it affects none, say so in one line and skip the checkpoint, but still run the validator.

## Decision batching

Do not resolve every edge case in a separate turn by default.

When several related rules can be judged independently, batch 3-7 of them into a compact decision table such as:

| Scenario / term | Proposed domain rule | Why it matters |
|---|---|---|
| ... | ... | ... |

Ask the user to approve the batch or correct specific rows. Apply approved rows immediately to the authoritative domain docs.

Use a single focused question only when one answer determines the next set of rules, or when the proposed rule would materially change the product scope.

After a rule is approved, derive obvious consequences without asking for redundant confirmation unless a consequence introduces genuinely new behavior.

### Quantitative invariant guardrail

Treat cardinality and numeric limits as consequential invariants. A count observed in discovery or an example must not become a domain maximum unless the approved scope explicitly establishes it or the user approves it during domain modeling. When the source of a proposed limit is unclear, surface it as an unresolved rule rather than encoding it. This is the domain-level form of product discovery's constraint inference guardrail.

Capture only durable domain truth, but classify it before persisting it:
- **Core invariant** — a cross-scenario business truth that must remain true regardless of UI or implementation.
- **State transition** — a lifecycle edge, trigger, guard, or terminal outcome for one domain concept.
- **Validation guard** — a local precondition for one action; keep it in the state model/spec unless it expresses a broader invariant.
- **Scenario outcome** — observable behavior for a concrete journey; keep it in `docs/specs.md` unless it reveals a new invariant.
- **Domain boundary** — include only when it clarifies different meanings or ownership of rules.

Do **not** promote every transition, validation guard, failure branch, notification, or scenario outcome into a numbered domain rule. Precision comes from the combination of a small invariant set + complete state models + behavioral specs, not from maximizing rule count.

## Domain passes

Use these as internal reasoning passes, not as a questionnaire:

1. **Language pass** — identify important nouns/actions in the approved journeys; resolve vague, overloaded, or duplicate terms.
2. **Rule pass** — extract what must always be true, who has authority, and what conditions make an action valid or invalid.
3. **State pass** — for every concept with a meaningful lifecycle, check creation, active states, allowed transitions, terminal states, and who/what may trigger each transition.
4. **Stress-test pass** — combine approved rules in concrete scenarios to find gaps: before/at/after time boundaries, cancellation/reversal, repeated actions, concurrent actors, recurrence/overlap, and interactions with quantities or money when relevant.

The lenses generate product-specific questions; do not mechanically ask about every lens. If a discovered ambiguity changes product scope or user-visible policy, route it back to product discovery rather than silently promoting it to a domain invariant.

**Noticeability test.** Before approving any rule, ask: *could a user notice this exists?* A new action, button, undo, reset, removal, or setting always passes this test. Such a rule is a product decision: get the user's approval, then add it to `docs/product.md` (journey summary or constraint) in the same change as `docs/domain.md`. The domain may define the rule's mechanics, but `product.md` must name the capability.

### Invariant promotion test

Before creating a numbered invariant, require at least one of these to be true:
1. it constrains multiple actions/states or must hold across more than one journey;
2. violating it would corrupt authority, money, ownership, identity, or another durable business truth;
3. downstream design/implementation needs it as a stable rule independent of a specific transition.

If the statement only answers **"when may this transition happen?"**, **"what state follows?"**, **"what does the user see?"**, or **"what validation error occurs?"**, keep it in the lifecycle/spec rather than promoting it.

For a normal small/medium product, aim for roughly **12–30 core invariants**. This is a diagnostic range, not a hard cap: a genuinely complex regulated/financial domain may justify more. If a small product exceeds ~30, perform a clustering pass before exit and explain why any excess is irreducible. Never merge unrelated rules merely to hit a number.

### State-model completeness audit

Before considering a lifecycle settled, ask whether the model explains:
- how the state is entered,
- which actors/events may move it,
- what happens at meaningful time boundaries,
- whether actions can be repeated safely,
- whether cancellation/correction/reversal exists or is intentionally unsupported,
- which states are terminal,
- and whether two approved rules produce conflicting outcomes when combined.

Do not invent transitions merely to make the model total. Surface genuine gaps for approval or upstream routing.

Maintain `docs/domain.md` as the durable domain source of truth. Organize it with clear sections for canonical language, **Core Invariants**, **State Models**, and boundaries. Keep it one file: the validator treats per-area domain files as a competing source of truth. Keep implementation details, frameworks, database choices, UI notifications, and journey narration out of the domain document.

Write domain truth densely, following `assets/domain-template.md`. The validator parses core invariants only in this exact form, one per line under `## Core Invariants`:

```
- **D-01 — <Short name>.** <The durable rule.>
```

Never write invariants as headings (`### D-01`); the validator rejects that.

- one-line canonical definitions;
- stable `D-xx` IDs only for **core invariants**;
- group related consequences into one invariant when they express one durable truth;
- put transition-specific guards/outcomes in compact state tables instead of giving each its own invariant ID;
- reference `docs/specs.md` for user-visible scenario detail rather than replaying it.

### Stable invariant-reference rule

Treat an approved `D-xx` as a stable downstream reference once `docs/specs.md`, `docs/implementation-plan.md`, tests, or code refer to it. Clustering an over-atomic domain model is allowed, but never silently renumber or reassign referenced invariant IDs.

When a domain refactor genuinely requires an identifier migration:
- build an explicit old -> new mapping;
- update every downstream `D-xx` reference in the same change;
- reject positional references such as `domain 12-18`, which become meaningless after clustering;
- run `python3 tools/validate-workflow.py` before approval.

Prefer keeping a stable invariant ID and refining its wording when the durable truth is unchanged.

Do not persist contradiction-discovery narrative or the phase-exit summary after approval unless it explains an otherwise surprising invariant. Soft target: **<= 16 KB** for a normal small/medium product; if it grows past that, compress classification mistakes and duplicate explanation.

Update domain docs as decisions crystallize rather than batching a second copy later. If behavior is still ambiguous, keep discussing it; do not invent a rule merely to complete the document.

Use tactical DDD patterns only when real domain complexity justifies them. The goal is shared language and correct rules, not ceremony.

## Phase-exit checkpoint

Before declaring domain modeling complete, present a compact checkpoint containing:
- **Canonical terms** — important terms introduced or clarified,
- **Classification count** — core invariants, lifecycle/state transitions, and material scenario/validation items intentionally routed to specs rather than promoted,
- **Core invariants** — count and short grouped summary; if >30 for a small/medium product, state why clustering could not reduce it,
- **State models** — meaningful lifecycles/transitions now defined and completeness audit passed,
- **Domain boundaries** — only if any were needed,
- **Contradictions found** — scope/spec/repo disagreements and their resolution,
- **Product backflow** — every user-noticeable capability introduced in this phase and the `docs/product.md` line that now names it, or `None`,
- **Unresolved domain questions** — must be `None` to exit,
- **Validation** — output of `python3 tools/validate-workflow.py` from the project root; must pass, warnings explained. If the tool is missing, run the project setup from product-discovery (files in `../product-discovery/assets/project-setup/`, relative to this skill's directory) first; never skip validation,
- **Exit criteria** — state whether the approved journeys can now be described without inventing business rules.

Ask for a single approval when new consequential domain decisions were made in the phase. If all rules were already settled upstream and this phase only normalized language, report the checkpoint without forcing ceremonial approval.
