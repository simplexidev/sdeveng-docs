# AgentTool command reference

`AgentTool` is the .NET 10 product CLI. Its file-based entry point delegates to the normal solution runtime. Run it from the toolkit
checkout, or use the Unix `sdeveng (legacy codex-agent-tool)` link installed with `--bin`. The checkout's
`help` output is authoritative for its version:

```console
dotnet tools/AgentTool.cs help
```

Use `--` after the source path when an option could be interpreted by the `dotnet` host.
`--root DIR` selects the target repository; `--toolkit DIR` selects a toolkit checkout;
`--json` requests structured JSON; and `--help` prints usage. AgentTool discovers its
checkout from the source path, then `SDEVENG_ROOT`, then parent directories. The legacy
`CODEX_TOOLKIT_ROOT` variable remains supported for v2 installations. It
writes bounded JSON, placing oversized rendered output and command artifacts under the
target's ignored `.agent-tool/` directory.

## Command groups

| Group | Commands | What they do |
|---|---|---|
| Lifecycle | `install`, `update`, `uninstall`, `doctor` | Plan or reconcile owned links; inspect required and optional prerequisites. |
| Repository | `repo changed-files`, `summary`, `locate`, `affected-projects`, `ownership`, `health`, `hygiene` | Derive change, path, MSBuild ownership, policy, and tracked-artifact facts. |
| Git | `git state`, `summary`, `conflict-forecast`, `prepare-commit`, `issue-start` | Inspect local state, forecast a merge, prepare a commit, or create an issue branch only after safety checks. |
| GitHub | `github pr-status`, `review-comments`, `prepare-pr`, `actions`, `labels` | Read bounded GitHub data and prepare local PR work. `actions --failed-logs` is for a selected run. `labels --apply` creates missing configured labels and updates their descriptions and colors without deleting unmanaged labels. |
| .NET facts and plans | `dotnet inspect`, `build-plan`, `test-plan`, `diagnostics-plan` | Inspect evaluated project facts or emit a narrow build, test, or diagnostics plan before execution. |
| .NET execution | `dotnet verify`, `format`, `dependencies`, `package-audit`, `api-check`, `release-verify` | Run targeted project checks. `format --apply` is the explicit formatting mutation; `api-check` requires an existing API-validation baseline. |
| Evidence | `logs summarize`, `sarif summarize`, `artifact inspect`, `artifact verify`, `test-results summarize`, `coverage summarize`, `verification decide` | Read and compact local artifacts; `sarif summarize --baseline PATH` identifies new and fixed findings. `verification decide` applies final PR evidence policy to an exact commit and can compare optional local evidence. |
| JEV | `jev noul`, `choice`, `score`, `screen`, `cache-clear` | Make a policy-bounded judgment or clear the local response cache. See [JEV](../concepts/jev.md). |
| Upstreams and product | `upstream status`, `update`, `dotnet-skills status|diff|check`, `validate`, `eval`, `release` | Inspect pinned integration metadata, validate product metadata/evals, or create a source archive. |
| Durable results | `results init`, `new`, `list`, `latest`, `context`, `clean` | Maintain the deliberately small cross-session result store. |

Useful exact forms include:

```console
dotnet tools/AgentTool.cs repo ownership --file src/App.cs
dotnet tools/AgentTool.cs git conflict-forecast --base origin/main
dotnet tools/AgentTool.cs github actions --run-id 123 --failed-logs
dotnet tools/AgentTool.cs -- dotnet test-plan --base main --filter "Category=Fast"
dotnet tools/AgentTool.cs -- dotnet diagnostics-plan --process-id 1234 --signal cpu
dotnet tools/AgentTool.cs results new handoff release-check
```

`dotnet test-plan` accepts one of `--test`, `--class`, `--category`, or `--filter` to
narrow selection. `dotnet diagnostics-plan` emits a collection command and environment
facts; it does not collect data itself. `release --output ZIP` packages the product after
its internal validation; it is not a GitHub publication command.

## Boundaries and exit status

AgentTool uses argument arrays rather than a shell and removes `TYPESAFE_API_KEY` from
child-process environments. Guarded commands can create owned commits and branches, push an exact owned branch,
create draft PRs, apply allowlisted labels, and request bounded CI reruns. They
never force-push, rewrite history, merge PRs, delete unrelated work, or create
arbitrary remote repositories. It can run builds, MSBuild evaluation,
GitHub CLI, package restore/audit, and formatting, so use it only against trusted targets
and review emitted plans before execution.

Successful commands return exit code 0; operational or validation findings may return a
nonzero code, and malformed input or command errors return 2. A JEV service fallback in
`required` mode returns 3 while retaining `REVIEW` semantics. Commands that need GitHub
require an authenticated, available `gh`; diagnostic optional tools are reported by
`doctor` and never installed automatically.
