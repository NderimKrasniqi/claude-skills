# claude-skills

My Claude Code skills, packaged as a plugin marketplace.

## Plugins

**product-workflow** — planning workflow for new products and large features. Run the skills in order:

1. `product-discovery` → `docs/product.md` (also sets up `AGENTS.md`, `docs/engineering-principles.md`, `tools/validate-workflow.py`)
2. `domain-modeling` → `docs/domain.md`
3. `journey-specification` → `docs/specs.md`
4. `solution-design` → `docs/architecture.md`
5. `implementation-plan` → `docs/implementation-plan.md` + `.workflow/tasks.json`

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
