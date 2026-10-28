---
name: product-discovery
description: "Think through a product idea, problem, or proposed feature with the user until the problem, solution, product journeys, constraints, and first useful scope are clear. Use before domain modeling or technical design."
metadata:
  version: "5.0"
---

# Product Discovery

Read and apply the [shared workflow contract](references/workflow-contract.md) before the skill-specific passes below. It defines cumulative decisions, completion evidence, minimal upstream correction and the planning/execution boundary. For direct filesystem/CLI operation only, use the [standalone host](references/cli-host.md); managed hosts supply those mechanics.


Act as an experienced product engineer thinking alongside the user.

Start from whatever they already know: a pain point, vague idea, concrete product idea, existing product, or proposed feature. Do not make them restart from first principles.

Have a natural conversation. Research facts yourself when the repo, supplied material, or available tools can answer them.

Recommend, challenge, and suggest when useful. The user owns consequential product decisions unless they explicitly delegate them.

**Delta mode.** For a change to an existing product, read `docs/product.md` first, discuss only what the change affects, keep existing journey IDs, and add new journeys with the next free ID. A small change that adds no journey, constraint, or scope still gets the stress-test and assumption audit, but the checkpoint may be a few lines.

## Discovery questioning loop

Use three adaptive modes. These are reasoning modes, not a questionnaire the user must fill out.

### Explore

Understand what the user is trying to accomplish, who experiences the problem, what outcome matters, and what solution direction they currently have in mind. Reflect the user's language and do not force premature detail.

### Probe

Once a direction is emerging, actively look for consequential gaps and assumptions implied by what has already been said. Ask clarifying questions that help the user think through things they may not have considered.

Generate questions from the specific product rather than from a fixed checklist. Useful lenses include:
- **behavior** — what might an actor reasonably do that has not been discussed?
- **lifecycle** — what happens before, during, after, when repeated, late, cancelled, or reversed?
- **authority** — who may view, decide, change, approve, or undo something?
- **boundaries** — is an example, current count, or default being mistaken for a product rule?
- **failure** — what happens when an action fails, expires, conflicts, or only partially succeeds?
- **interaction** — do two individually sensible rules behave strangely when combined?
- **external reality** — does the product assume a third-party platform, policy, device, or service behaves a certain way? Include the obligations that come with each channel or data type the product uses: opt-out and consent for SMS/email/push, rules for payments, retention and deletion of personal data. Any that a user would notice is a product decision to settle here.

Only ask about the lenses that are relevant; do not mechanically ask one question per lens. But consider every lens: the checkpoint reports each one as covered or not relevant.

### Stress-test

Before declaring discovery complete, walk through a small number of realistic end-to-end scenarios in slow motion. Include at least one ordinary success path and, when relevant, one boundary/failure/concurrency/reversal path.

Use the walkthroughs to discover missing decisions, contradictions, and hidden assumptions. Do not show every internal walkthrough to the user; surface only material gaps as a compact decision batch.

After each meaningful answer batch, ask yourself: **Given everything known now, what could still surprise us?** Continue only when the answer exposes a material product gap.

## Decision batching

Prefer **batched decision-making** over a long sequence of micro-confirmations.

When 3-7 related uncertainties can be understood independently, collect them into one compact decision set or table. For each item, state the proposed decision and only the rationale needed to judge it. Ask the user to approve the set or correct specific rows.

Use one-question-at-a-time only when:
- the answer materially changes which questions should come next,
- the decision is unusually consequential or sensitive,
- the user is still exploring the problem and a batch would force premature assumptions.

Do not ask the user to confirm obvious consequences of an already-approved rule individually. Fold those consequences into the next decision batch or checkpoint.

Help the conversation converge on:
- the real problem and who experiences it,
- the outcome they want,
- the solution direction and why it should help,
- the smallest useful product,
- end-to-end product journeys,
- durable product constraints that materially shape the product.

Use temporary delivery realities such as time, budget, and team size when they materially change what the first useful release should contain, but do not preserve them as durable product truth unless the user wants them recorded.

### Constraint inference guardrail

Do not turn facts about the user's current situation, examples, sample data, or observed counts into system-wide product constraints unless the user explicitly chooses that constraint or it is externally required.

Examples:
- "I have two children" describes the current household; it does **not** imply "a household supports at most two children."
- "We have three stores today" does **not** imply a three-store product limit.
- A sample workflow with one approver does **not** imply that multiple approvers are forbidden.

Before recording any numeric/cardinality limit as durable product truth, identify its source: explicit user decision, policy/regulation, commercial packaging, or a justified technical/product constraint. If none exists, model the product without that arbitrary cap.

Think in journeys rather than feature inventories. A journey is an end-to-end outcome an actor comes to the product to accomplish. Suggest candidate journeys when they emerge, but treat them as proposals until the user agrees.

### Journey topology and ordering audit

Before assigning stable journey IDs in a new product scope, map the journeys as a lightweight producer/consumer graph:
- what user-visible state or capability each journey creates,
- which later journeys depend on or consume that state,
- which journeys are genuinely parallel rather than sequential.

Prefer a causal lifecycle order where practical: **setup/access -> configure/create -> generate -> commit -> perform -> resolve -> downstream effects -> payment/settlement -> administration/recovery**. Adapt the sequence to the actual product rather than forcing every category to exist.

A journey that creates a prerequisite state should normally appear before a journey that consumes it. If two journeys are independent, order them for readability rather than inventing a dependency.

For a brand-new scope, perform this audit **before** assigning `J-xx` IDs. Once a journey ID has been approved and used downstream, treat it as a stable reference: do not renumber it merely because presentation order later changes. If an existing scope has materially misleading ordering, surface a deliberate identifier migration rather than silently rewriting references.

This skill owns journey order. Downstream skills rely on it and do not re-derive it.

Keep discovery product-focused. Technical design belongs later.

Occasionally summarize your current understanding so the user can correct it. Prefer one consolidated summary over repeated per-rule confirmations.

## Assumption audit before exit

Before presenting the phase-exit checkpoint, inspect every consequential statement you are about to preserve and classify its source internally as one of:
- **user-stated** — explicitly supplied by the user,
- **user-approved** — proposed and explicitly accepted,
- **derived** — a necessary consequence of already-approved product rules,
- **assumed** — introduced by the agent without sufficient approval or external authority.

No consequential **assumed** statement may silently enter `docs/product.md`. Either ask about it, remove it, or explicitly hand it to a later phase when it is genuinely a domain/technical question rather than a product decision.

Also distinguish unresolved **product** decisions from intentional downstream handoffs. Product decisions capable of changing the first useful scope must be settled before exit; detailed domain mechanics and technical implementation questions may be listed as handoffs instead of being forced into discovery.

## Phase-exit checkpoint

Complete the discovery reasoning before handing off. Within an authorized continuous workflow, the host moves to domain modeling using the same answers; do not ask the user to start another stage.

When discovery appears complete, present one compact checkpoint containing:
- **Problem** — the problem and target actors,
- **Solution** — the agreed solution direction,
- **Product journeys** — stable journey IDs/titles when available,
- **Material constraints** — only durable product constraints,
- **Out of scope** — important exclusions that prevent accidental expansion,
- **Unresolved product decisions** — must be `None` to exit,
- **Downstream handoffs** — domain or technical questions intentionally left for later skills, if any,
- **Assumption audit** — list each agent-proposed statement being preserved with its source (user-approved or derived from which rule); `None assumed` must be literally true,
- **Stress-test** — one line per walkthrough scenario and what it changed, if anything,
- **Lens coverage** — each of the seven lenses: what it surfaced, or why it does not apply to this product (one line each),
- **Exit criteria** — explicitly state whether problem, solution, journeys, constraints, and first useful scope are clear and have survived the stress-test.

Use the shared completion contract. Obtain judgment only for consequential product choices still unresolved; do not seek another phase approval for choices already accepted. If the user corrects an item, revise that item and affected relationships without restarting discovery.

Create or update the evolving `docs/product.md` draft with `assets/product-template.md`, preserving unaffected scope and stable journey IDs. Each journey is a `### J-01 — <name>` heading. Completion requires evidence for the criteria above and the host's structural checks. Keep source status Draft unless the owner explicitly approved that artifact; completion alone does not imply approval.

### Durable-doc compression rule

`docs/product.md` is a product contract, not a transcript. Preserve only the problem/solution, compact `J-xx` journey summaries, durable constraints, first useful scope, material exclusions, and necessary downstream ownership references. Do not repeat detailed domain rules or scenario flows that belong in later artifacts. Prefer one-line journey summaries and references. Soft target: **<= 8 KB** for a normal small/medium product; if larger, remove duplication before adding sections/files.
