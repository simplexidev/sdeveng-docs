# Develop the product

For the complete contributor workflow across the product, docs, and metrics repositories,
start with the [five-repository development workflow](workflow.md).

Product code, skills, runtime references, templates, tests, and releases belong in
[`simplexidev/sdeveng`](https://github.com/simplexidev/sdeveng). Human prose belongs here
in `sdeveng-docs`; evaluator, versioned data, and dashboard work belong in the respective
`sdeveng-metrics-tooling`, `sdeveng-metrics-data`, and `sdeveng-metrics-dashboard`
repositories.

The product requires .NET 10. From its checkout, run:

```console
dotnet test tests/SdevEng.Tests/SdevEng.Tests.csproj
dotnet tools/AgentTool.cs validate
dotnet tools/AgentTool.cs eval
git diff --check
```

Tests use temporary homes, temporary Git repositories, and fake HTTP responses. Never
install tests into your real Codex profile or use a live billable JEV call in normal test
or CI execution.

Production logic belongs to the .NET solution: Core owns deterministic contracts and
decisions, Infrastructure owns external adapters, and Cli owns startup and command
modules. `tools/AgentTool.cs` is the direct-invocation compatibility launcher for the
CLI. See [the architecture overview](../architecture/index.md#product-components) for
the component and repository boundaries. Add focused tests for changed behavior, keep
config and schemas synchronized, and give skills narrow triggers with observable
regression scenarios. Standard parsers in tests are preferable to approximate homegrown
parsing; they are not runtime dependencies.

For release changes, verify all manifests and `config/toolkit.json` share the intended
version, run the full gate, and create a version tag only with explicit authorization.
The GitHub workflow creates a draft release for human review and a checksum; it does not
turn local caches or result stores into release artifacts.
