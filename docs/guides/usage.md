# Common workflows

Run AgentTool from the toolkit checkout or use `sdeveng (legacy codex-agent-tool)` after Unix launcher
installation. Add `--root /path/to/project` when the target is not the current directory.

```console
# Understand a change
dotnet tools/AgentTool.cs repo changed-files --base main
dotnet tools/AgentTool.cs repo affected-projects --base main

# Validate a .NET change
dotnet tools/AgentTool.cs -- dotnet verify --project tests/MyTests.csproj

# Compact large evidence
dotnet tools/AgentTool.cs -- logs summarize --file build.log
dotnet tools/AgentTool.cs sarif summarize --file results.sarif

# Check Git and prepare a handoff
dotnet tools/AgentTool.cs git prepare-commit
dotnet tools/AgentTool.cs results init
dotnet tools/AgentTool.cs results new handoff feature-review
```

The separator `--` prevents the `dotnet` host from consuming AgentTool options such as
`--project` or `--help`. Output is compact JSON in default and `--json` modes. AgentTool
may write overflow details to ignored `.agent-tool/` paths, and can perform explicit owned Git/GitHub mutations after safety and capability
checks. It never force-pushes, rewrites history, merges PRs, deletes unrelated
work, creates arbitrary remote repositories, or installs external tools.

Use `.agent-results/` only for durable, non-obvious state that must cross independent
sessions. Audits, handoffs, reviews, and reports are tracked; generated logs, traces,
SARIF, binlogs, test output, and temporary files are ignored. Link large evidence by path
instead of pasting it into a handoff.
