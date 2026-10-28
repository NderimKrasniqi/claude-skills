# claude-skills

My Claude Code skills, packaged as a plugin marketplace.

## Plugins

**product-workflow** — planning workflow for new products and large features. The skills develop one cumulative planning conversation:

1. `product-discovery` → `docs/product.md` (standalone CLI setup supplies missing workflow files without replacing project policy)
2. `domain-modeling` → `docs/domain.md`
3. `journey-specification` → `docs/specs.md`
4. `solution-design` → `docs/architecture.md`
5. `implementation-plan` → `docs/implementation-plan.md` + `.workflow/tasks.json`

## Using the workflow

When planning a new product or a large feature, each skill builds on settled decisions and current upstream documents. Consequential choices need judgment; routine synthesis and phase completion do not need another approval. Useful drafts evolve during the conversation. Each skill reports completion evidence, open choices and the smallest upstream correction when needed.

The shared workflow contract separates methodology from hosting. A standalone CLI uses the conditional setup/validator reference; ai-os supplies persistence, staged documents, validation and automatic handoffs. Planning finishes with one reviewable build plan. Implementation starts only through separately authorized execution, such as Start factory.

The default engineering assets preserve existing folder ownership, cohesive responsibilities, purposeful comments and applicable stack practices. They specify static/build checks and focused feature E2E, with no default unit/integration suites or TDD. Existing project policies and tests are preserved.

## Install

Once per machine:

```bash
claude plugin marketplace add NderimKrasniqi/claude-skills
```

Then, inside the project that should use it:

```bash
claude plugin install product-workflow@nderim-skills --scope project
```

Skills are invoked as `/product-workflow:product-discovery`, etc.

## Update

```bash
claude plugin marketplace update nderim-skills
```
