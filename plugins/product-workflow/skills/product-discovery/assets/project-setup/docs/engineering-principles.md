# Engineering Principles

## Before implementation

Read this policy with the task contract, domain rules and architecture. Inspect the existing folders, responsibility owners and similar code before choosing file locations. Reuse established structure. A new directory needs a distinct responsibility that existing owners cannot accommodate; do not create parallel utilities or split files to meet arbitrary length targets.

## Implementation and review

The implementer owns readable, maintainable code before handoff. Use clear domain names, cohesive responsibilities, deliberate dependency direction and the simplest design that satisfies the agreed behavior. Keep related private types and helpers with their owner; separate unrelated concerns. Follow applicable practices for the installed stack and the project's conventions. Address relevant input/trust boundaries, failures, state consistency, concurrency, resource lifetimes, accessibility and performance.

Comments explain non-obvious reasons, constraints, workarounds or required directives. Prefer clear names and structure to narrating comments, obvious documentation or change diaries. Review complete changed files, callers and directory placement before handoff. Avoid stylistic churn when code is already clear and correct.

## Verification

Do not generate unit or integration test suites or impose TDD by default. Respect an existing project's explicit testing policy and suites; identify a concrete conflict rather than removing existing tests.

Run applicable type, build and static checks. After completing a feature, verify its observable journey and meaningful edge behavior with focused E2E. Do not duplicate assertions merely to fill test layers or chase coverage percentages. An enabling prerequisite may defer feature E2E only with an explicit reason and a named dependent feature that covers it under the host/owner-approved verification boundary. The release gate retains feature E2E.

Report commands/scenarios actually run, results and blocked verification. Do not infer test success from model confidence, planning completion, a source checkbox or a process fixture. Evidence must match the current specification and code revision.

## Review responsibilities

Engineering-quality review evaluates responsibilities, readability, maintainability, folder placement, abstraction and applicable stack practices. Correctness review evaluates behavior, omissions, edge cases and relevant security concerns. Use an additional focused security assessment when the actual change warrants it; do not manufacture a separate approval workflow for unrelated low-impact work. Keep findings concrete and unresolved material issues visible.

## Living context

Planning may use broad context. Implementation starts from a focused task contract, its relevant J/D/ADR references, engineering policy and existing code; expand for a concrete ambiguity. Do not invent a missing consequential decision while coding. Route the smallest correction to the owning product, domain, specification or architecture artifact, preserving stable IDs and unaffected content.

## Ready and done

Ready means behavior can be implemented without inventing a material rule, dependencies have actual accepted code, and required verification is feasible in the available environment. Done means agreed behavior is implemented, applicable checks and feature E2E have current evidence, affected docs agree, and no unresolved material quality/correctness/security issue remains. Acceptance, integration and deployment follow the explicitly authorized host governance; planning alone grants none of them.
