# Product-workflow contract

Apply this shared contract with the active skill. The skill owns its reasoning passes and document truth; the host owns persistence, output transport, permissions, validation execution and orchestration. A host contract may adapt those mechanics, but cannot turn an unresolved assumption into an accepted product rule. Use only the user's authorized scope and delegated authority.

## One conversation, cumulative decisions

Start from existing documents, answers and decisions. Do not restart an interview at a phase boundary or ask the user to type a phase-start command. Maintain one understanding as product discovery, domain modeling, journey specification, solution design and implementation planning develop it. Host handoffs are context, not new owner decisions. Superseded versions remain history, not current requirements.

Distinguish user-stated facts, explicitly accepted proposals, necessary consequences of accepted rules, externally evidenced constraints, and assumptions. Record the basis for consequential rules in the handoff evidence. An agent recommendation, source status label or completion flag is not owner approval. Do not infer a global limit from a current count, example or sample workflow.

## Consequential choices, once

Ask for judgment when a choice materially changes scope, business rules, observable behavior, cost, security/privacy, a costly-to-reverse design commitment, or execution authority, unless the user has already delegated that choice. Show the recommended choice, viable alternatives and concrete consequences. Combine related independent choices when helpful; use one question when its answer changes what follows. Leave free-text correction possible.

Not every observable detail is a consequential product choice. Use existing project/stack conventions for routine protocol, presentation and naming details that preserve the agreed capabilities, rules, authority, meaningful success/failure guarantees, cost and security boundaries. Document the chosen default briefly where it belongs and leave it revisable; do not promote it to an owner-approved product rule or create a decision gate solely because a client could observe it. Escalate when alternatives actually change those material boundaries or existing client commitments, not simply because several technically valid defaults exist.

Reuse accepted answers. Do not ask again for an obvious consequence, normalized terminology, wording, routine synthesis or merely because a phase ended. If an accepted decision must change, identify the new evidence and affected behavior. A completed phase reports its result and next handoff; it does not manufacture a fresh approval gate.

## Drafts and completion evidence

Produce useful evolving drafts while questions remain, using the active skill's artifact format. Keep unresolved assumptions out of settled truth. Do not mark a phase complete merely because a file exists or because all section headings are present.

At handoff, provide these semantics in the host's response format, or a concise labeled summary in a plain CLI conversation:

- **State:** in progress, blocked on a named input/correction, or complete.
- **Artifacts:** exact paths and whether each is drafted, changed, unchanged or superseded. Include complete content only when required by the host.
- **Evidence:** the active skill's exit criteria, with concrete witnesses: accepted answers, scenario/outcome checks, resolved IDs, producer/dependency relationships, inspected source locations or actual tool results. A statement such as “all checks passed” without witnesses is insufficient.
- **Open decisions:** only consequential choices still requiring judgment, or none. Distinguish them from nonblocking downstream handoffs.
- **Upstream correction:** the precise gap and affected owners/IDs, or none.

Complete means every applicable exit criterion has evidence, no material decision or invalidating unknown is unresolved, references agree with current upstream truth, and the available structural validation passed. Report unavailable validation as blocked or explicitly deferred to the host, never as a pass. Reasoning walkthroughs are not executed tests; a design claim is not inspected evidence.

Keep evidence proportionate. Retain detailed audits in host-visible evidence or the handoff rather than bloating the canonical product documents. For an unchanged delta, state why it is unaffected, validate the relevant relationships, and continue within the authorized workflow without a ceremonial checkpoint.

A JSON host may map these semantics into its existing fields. For example, ai-os uses message for completion evidence/open gaps, files for current-phase artifacts, phaseComplete for readiness and decision for a consequential choice. Do not add unsupported response keys or treat that mapping as permission to call runtime endpoints.

## Small upstream corrections

When later reasoning reveals a missing rule or contradiction, stop only the work that depends on it. Return:

1. The concrete scenario or observed evidence exposing the gap.
2. Its owning artifact/skill and exact affected J/D/ADR/task IDs or named section.
3. The smallest proposed correction and any consequential choice needed.
4. Which downstream artifacts/dependencies require rechecking and which remain unaffected.

Do not restart the whole product, renumber stable IDs, silently rewrite upstream files from a downstream assignment, or fill the hole with an implementation assumption. Use the host's reopen/change mechanism. If an authorized standalone change spans owners, reconcile the smallest affected set together, then resume from the preserved context. Historical implementation evidence does not transfer to changed contracts.

## Planning versus execution

Planning completion does not start agents, approve code, integrate branches, deploy software or waive governance. Present the complete build plan once for the owner's execution decision; allow corrections to order, granularity and verification boundaries without task-by-task approval. Record Approved only after an actual approval covering that artifact or plan. Otherwise keep Draft even when planning reasoning is complete.

The host may continue an already-authorized planning workflow using the next available skill. A single-skill request remains within that scope. If the next skill or tool is unavailable, report the exact handoff instead of pretending it ran. Execution starts only through the user's separately authorized mechanism, such as ai-os Start factory with explicit limits.

Read the project's engineering policy before design/planning. Preserve existing folder ownership, purposeful comments, cohesive responsibilities and applicable stack practices. Do not introduce unit/integration suites or TDD by default. Use applicable static/build checks and focused E2E after a completed feature. A prerequisite may defer feature E2E only with a reason and a named dependent feature that covers its behavior; the release gate retains E2E. Such a deferral needs the host's/owner's explicit verification-boundary approval, not an agent waiver.
