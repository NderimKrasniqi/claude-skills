---
name: solution-design
description: "Design the technical solution after product journeys and behavior are understood. Use to choose/refine the tech stack and maintain one compact architecture source with only consequential ADR sections."
metadata:
  version: "4.0"
---

# Solution Design

Read and apply the [shared workflow contract](../product-discovery/references/workflow-contract.md) before the skill-specific passes below. It defines cumulative decisions, completion evidence, minimal upstream correction and the planning/execution boundary. For direct filesystem/CLI operation only, use the [standalone host](../product-discovery/references/cli-host.md); managed hosts supply those mechanics.


## Entry gate

Require current journeys/specs and domain truth grounded in settled owner decisions, sufficient to describe the release behavior without technical design inventing product rules. If the behavioral contract is incomplete or contradictory, route the gap upstream before choosing architecture around it.

Design the smallest technical solution that supports the approved behavior.

Inspect the existing code/design first when working in an existing product. Recommend options and trade-offs; the user owns consequential choices unless delegated.

**Delta mode.** For a change to an existing product, re-run the design passes only for the affected journeys. If no component, boundary, or decision changes, report the unchanged delta and validate affected relationships through the host; no phase approval is needed.

## Decision batching

Group related technical choices into coherent decision sets instead of seeking approval service-by-service or library-by-library.

For a decision set, present only viable options and compare them against the product's actual constraints. A compact table is preferred when several choices are independent.

Examples of useful batches:
- client framework + navigation + styling,
- backend + persistence + realtime/scheduling,
- authentication + session storage + authorization boundary,
- external integration boundary + fallback behavior.

Ask for one approval per consequential decision set. Do not treat a recommendation as selected until the user approves it unless they explicitly delegated the choice.

Use one-at-a-time discussion when one technology choice materially constrains the viable options for the next choice.

## Design passes

Use these as internal coverage passes before closing design:

1. **Capability map** — map each approved journey to the technical capabilities and authoritative state it needs.
2. **Authority/trust pass** — identify which component is authoritative for identity, authorization, time, money/quantities, and external outcomes; never leave the client authoritative for a server-owned invariant. For transient credentials (one-time codes, magic links, invite tokens), define their full lifecycle: entropy, disclosure scope, storage at rest, expiry, single use and replay, attempt/rate limits, revocation, and what gets logged. A low-entropy credential must be protected by explicit attempt limits and expiry, never by convention.
3. **Quality/risk pass** — inspect only relevant quality attributes such as security/privacy, reliability/idempotency, concurrency/time, offline/network behavior, native/platform constraints, operability/observability, data lifecycle, and material scale/performance.
4. **External-boundary pass** — define failure, retry, cancellation, timeout, and confirmation semantics for integrations that the product depends on. Also list obligations an integration or regulation imposes (e.g. SMS opt-out/STOP handling, consent, data retention and deletion). Any that a user can notice must become approved behavior in the specs via the behavioral backflow rule, not only an architecture note.
5. **Unknowns pass** — identify assumptions that could invalidate the plan and resolve them with inspected docs/evidence or a narrow technical investigation. Name the source or experiment and what it established. Reasoning alone cannot prove a third-party API or runtime behavior.

These are lenses, not mandatory architecture sections. Do not add infrastructure or documentation solely to satisfy a lens when the product does not need it.

Maintain `docs/architecture.md` as the single current-state technical source of truth, starting from `assets/architecture-template.md`. Keep a **Technology Stack** section for current choices and a **System Architecture** section for major modules, dependency direction, ownership, external boundaries, important flows, and cross-cutting constraints. Prefer updating the relevant section over creating another design file.

Keep `docs/architecture.md` operational: current stack, system map, data/authority boundaries, key flows, security/runtime constraints, verification gates, and a compact **Architecture Decisions** section. Product behavior and library tutorials do not belong here. Soft target: **<= 16 KB**; compress superseded ADR prose before splitting.

Append an `ADR-xx` subsection under **Architecture Decisions** in `docs/architecture.md` only when all three are true:
1. the decision is costly to reverse,
2. a future reader would reasonably ask why it was made,
3. there were genuine alternatives/trade-offs.

Each consequential decision gets a stable `### ADR-xx — …` heading inside `docs/architecture.md`. The heading is the address; do not add HTML anchors or hidden comments. Keep active ADRs compact: **Decision / Why / Consequences / Rejected alternatives**. When a decision changes, update the current architecture, add a new ADR, and reduce the superseded ADR to a compact `Superseded by ADR-xx` stub when its old rationale is no longer operationally useful. Git carries the full historical diff; do not create a separate decision-history file.

Surface technical unknowns that could invalidate later work. If an unknown cannot be settled by reasoning/docs, recommend a narrow technical investigation before dependent implementation phases.

### Behavioral backflow rule

Technical design/investigation may reveal that the product needs a new user-visible prerequisite, actor action, permission, confirmation, failure state, or fallback that the approved specs never defined. Treat that as **new behavioral information**, not permission for architecture or the implementation plan to invent a feature.

Examples include an external integration proving that the user must first configure recipient/contact data, or that a previously assumed automatic confirmation actually requires a manual confirmation step. Route the smallest change back to product discovery/journey specification, obtain the appropriate approval, then update the current design.

Before solution-design exit, compare each externally visible technical flow with the approved journeys and confirm that design introduces no unapproved user-facing behavior.

## Phase-exit checkpoint

Do not begin implementation planning until solution design has a clear closure point.

Present one checkpoint containing:
- **Approved tech stack** — current choices only,
- **Architecture** — major components, ownership, dependency direction, and authoritative/trust boundaries,
- **External boundaries** — integrations and authoritative/non-authoritative responsibilities; any newly discovered user-visible prerequisite/fallback has been routed into the behavioral specs,
- **ADRs** — compact accepted/superseded `ADR-xx` sections inside `architecture.md`,
- **Technical investigations** — completed evidence and any implementation-time verification still required,
- **Unresolved technical unknowns** — must contain no item capable of invalidating the implementation plan; relevant quality/risk and external-boundary passes completed,
- **Validation** — the host's actual structural/reference results, with warnings explained; report unavailable checks as pending rather than inventing a pass.
- **Exit criteria** — state whether the design is sufficient to plan dependency-safe implementation tasks without inventing architecture during implementation.

Use the shared completion contract. Ask only for consequential design choices still unresolved or changed by new evidence; do not ask again for the accepted stack or a synthesized architecture. Plan-invalidating unknowns block completion. Otherwise report evidence and hand off to implementation planning within the authorized workflow.
