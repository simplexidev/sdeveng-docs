# Source audience and migration manifest

## Current review record

This is a current ownership and consumer check, not a migration plan. The active product
identity is SimplexiDev Engineering Toolkit (`sdeveng`). The canonical repository identities are
`sdeveng`, `sdeveng-docs`, `sdeveng-metrics-tooling`, `sdeveng-metrics-data`, and
`sdeveng-metrics-dashboard`. The former `codex-toolkit*` URLs redirect through GitHub's
supported rename behavior. This record was reviewed
against the candidate product revision `5a4b35e` on `develop/v2.0.0` and the candidate
metrics revision `8d75907` on `main`. It supersedes the initial import inventory, whose
historical paths and counts are not a reliable description of the current product.

The product currently has one unified plugin with 29 skill directories, one optional
project template, and one native `reviewer` agent. Counts are descriptive only; the
product manifests and validation are canonical.

## Ownership and consumers

| Material | Owner | Consumer boundary | Human-doc treatment |
| --- | --- | --- | --- |
| `SdevEng.slnx`, `src/`, `tools/AgentTool.cs`, `config/`, `schemas/`, installers, tests, and release workflow | `sdeveng` | Runtime, tests, and release tooling | Explain behavior here; link to the product for the canonical source and schemas. |
| `plugins/sdeveng/skills/*/SKILL.md` | `sdeveng` | Codex skill discovery and active agent workflows | Keep product-local. The [skills catalog](reference/skills.md) is an index, not a replacement. |
| `plugins/sdeveng/skills/jev-judgment/references/` | `sdeveng` | Lazily loaded by the JEV skill through product-relative paths | Must remain product-local. [JEV and bounded judgment](concepts/jev.md) is a separate explanation for people. |
| `global/AGENTS.md` and `agents/reviewer.toml` | `sdeveng` | Installed Codex instructions and native-agent discovery | Keep exact runtime files in the product; summarize their roles in human prose only. |
| `templates/project/` | `sdeveng` | Explicit, user-copied project customization | Keep templates canonical in the product; explain selective adoption in [project integration](guides/project-integration.md). |
| Product `README`, legal notices, and release notes | `sdeveng` | Product landing, distribution, and GitHub conventions | Keep product copies canonical; this site links or explains rather than relocating them. |
| Evaluator, scenarios, schemas, and measurement code | `sdeveng-metrics-tooling` | Offline measurement and validation | Keep implementation separate from published data and presentation. |
| Sanitized, versioned aggregate history | `sdeveng-metrics-data` | Validators and dashboard publication | Never commit raw runs, prompts, responses, private source, or logs. |
| Static dashboard and Pages workflow | `sdeveng-metrics-dashboard` | Human metrics readers | Consume only manifest-approved data; keep tooling implementation out. |
| Installation, use, configuration, architecture, security, examples, and contributor guidance | `sdeveng-docs` | Human readers only | This repository is never loaded by the runtime. |

## Guardrails for future changes

Before moving or deleting Markdown from the product, search product code, skills, agent
definitions, tests, workflows, manifests, and release packaging for consumers. Markdown
is not automatically human-only: a compact reference loaded by a skill or agent remains a
runtime file even when a fuller human explanation belongs here.

In particular, do not replace a product JEV reference with a link into this repository.
The runtime reference must be self-contained and product-relative; this site may explain
the same safety boundary in different, reader-oriented language.

Do not copy raw result-store content, prompts, responses, private source, local paths,
or unsanitized logs into these pages. Metrics documentation may describe methodology and
link to the dashboard, but it must not become the evaluator, dashboard, or data source.

When ownership or an active consumer changes, update this record and the affected human
navigation in the same documentation phase.
