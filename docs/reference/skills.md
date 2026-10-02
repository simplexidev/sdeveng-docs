# Skills catalog

The unified plugin currently contains 29 narrowly triggered skills. A skill is routing
and workflow guidance, not an authority grant or an implicit installation of an external
tool. Runtime `SKILL.md` files and their compact references stay canonical in the product
repository; this is the human index.

| Skill | Intended role |
| --- | --- |
| `address-pr-review`, `prepare-commit`, `finish-pr`, `issue-start`, `roadmap-next` | Scoped review-feedback, commit, PR, issue-branch, and next-work workflows. |
| `repo-health`, `docs-impact`, `architecture-change` | Repository policy, documentation impact, and component-boundary analysis. |
| `run-dotnet-tests`, `write-dotnet-tests`, `dotnet-test-quality`, `dotnet-coverage` | Narrow test planning/execution, test authoring, quality review, and coverage interpretation. |
| `diagnose-build`, `optimize-build`, `diagnose-dotnet`, `investigate-dotnet-performance`, `benchmark` | Build, MSBuild, runtime, measured performance, and explicit benchmark work. |
| `dotnet-format`, `dependency-change`, `package-audit`, `api-compatibility`, `reproducible-build`, `release-verify`, `sbom`, `versioning` | Focused engineering and release gates. |
| `ci-triage`, `security-scan` | GitHub Actions and deterministic security/SARIF evidence. |
| `jev-judgment` | A bounded semantic tie-break after deterministic narrowing. |
| `agent-maintenance` | Owned-link, instruction, agent, and toolkit configuration maintenance. |

Discovery is intentionally selective: Codex matches skill metadata to the task, then the
skill directs a small workflow. Detailed runtime references load only when the selected
skill and evidence call for them—for example, MSBuild diagnostics, test framework edge
cases, or JEV calibration. Broad repository maps, binary traces, dumps, and every skill
are not meant to enter ordinary context.

Many skills have an `agents/openai.yaml` descriptor, but the active behavior lives in the
matching `SKILL.md`. The product's `validate` command checks runtime references and
metadata; product `eval` is an offline scenario-integrity check. See
[skill authoring](../development/skill-authoring.md) for contribution rules.
