# Standalone CLI host

Read this only when the agent directly owns project files and validation. A managed host that supplies persistence, a read-only planning workspace or its own validator controls those operations instead. Never run this setup inside a managed discovery turn.

## Setup without replacing project policy

Inspect the existing root, instructions, docs, tools and relevant folder conventions first. Reuse established responsibility owners. The bundled [project setup assets](../assets/project-setup/) are fallback assets for a project that lacks the corresponding responsibility, not a migration to impose on an existing project.

- For a missing project map, use `AGENTS.md`. If it exists, add only missing workflow pointers where they fit; do not paste a second copy of project rules or change existing instructions.
- Copy `docs/engineering-principles.md` only when no existing engineering policy owns the responsibility. Preserve customized policies; surface a concrete conflict instead of replacing them.
- Reuse an existing workflow validator where suitable. If absent and authorized file creation is available, copy `tools/validate-workflow.py` from the setup assets. Do not overwrite an existing checker.
- Do not create a provider-specific instruction file solely to run these skills. Inspect the actual instruction setup; do not assume one provider's discovery behavior applies to another. Where an existing provider instruction file needs a project-map link, add only that pointer using its supported syntax and preserve other content.

Report created/extended files briefly. Do not install plugins, alter model/provider settings or change permissions as an inferred setup step.

## Drafting and validation

Write complete evolving drafts in the owning canonical files, preserve unaffected content and stable IDs, and keep unapproved choices out of settled truth. File writes do not approve a phase or authorize implementation.

After changing authoritative planning documents, run the project's structural workflow checker. For the bundled checker:

```sh
python3 tools/validate-workflow.py
```

An evolving draft may still lack agreed stable IDs or complete relationships. Report those failures as draft incompleteness and keep the phase in progress; do not invent decisions or assign premature IDs merely to make a checker pass. Blocking structural failures must be resolved before completion.

The checker validates currently present documents and their relationships; it is not a product correctness or execution test. Fix format/reference defects without changing accepted behavior. Explain warnings. If setup or execution is unavailable, state the blocker and leave validation pending rather than claiming success. A managed host may validate staged proposals instead of running this command.

Use the shared workflow contract for completion and upstream correction. Keep the conversation continuous within the user's authorized planning scope. Before implementation, present the complete build plan for the owner's execution decision; do not launch code work from a planning checkpoint.
