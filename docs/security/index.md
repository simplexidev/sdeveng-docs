# Security, credentials, and privacy

The toolkit is designed to reduce accidental mutation and data exposure, not to defend
against a malicious process running as the same user. Keep the checkout, target
repositories, and user profile trusted.

## Threat model and trust boundaries

The practical model assumes the operating-system account, Codex installation, toolkit
checkout, and repositories selected for execution are trusted. The toolkit adds guardrails
against common operator and automation mistakes: shell interpolation, unbounded output,
unsafe Git state, redirected installation paths, accidental external transmission, and
deleting files it does not own.

It does not sandbox hostile repository code, protect against a compromised dependency or
toolchain, prevent a same-user process from reading files or memory, replace endpoint
security, or decide whether organizational policy permits sending data to a provider.
Codex sandboxing and approval policy remain separate controls.

## Execution and Git boundaries

AgentTool launches child processes with argument arrays rather than a shell, redirects
their output, applies timeouts, and removes the JEV credential from their environments. An
argument is also rejected if it contains the current JEV key. These controls reduce
accidental disclosure and quoting hazards; unrelated ambient credentials required by the
selected tool remain the user's responsibility.

Branch creation requires a clean, attached repository with no in-progress Git operation
and an appropriate open issue. The read-only PR preparation command does not publish. Separate guarded
commands can create an owned commit, push an owned branch, and create a draft
PR. They do not merge or force-push.
GitHub commands use the user's existing `gh` authentication and inherit its permissions.

MSBuild and build commands may execute project or imported logic. Run them only for
trusted repositories. Optional tools and upstream integrations are not installed
automatically.

## Installation boundary

The installer records exact links and removes only matching, owned entries. It refuses
symlinked state and destination parents to reduce redirected writes. Replacements and
unrelated files are preserved. A same-user filesystem race remains outside this model.

## The JEV credential

`TYPESAFE_API_KEY` is the only application-level secret input. AgentTool accepts it only
from its own environment, removes it from every child-process environment, and does not
accept it in arguments, configuration, request JSON, logs, cache entries, or structured
output. Only the JEV HTTP path can apply it as bearer authentication, and redirects are
disabled.

Inject the key only into the specific AgentTool process or a narrowly scoped terminal
session. Secret storage and injection are outside the toolkit. Do not put it in a
repository, `.env`, JSON, shell profile, `environment.d`, desktop-wide environment, or
the input payload. `doctor` reports only whether JEV credentials are available.

Normal tests and CI are keyless and use fake HTTP responses. Any live GitHub check should
use the dedicated protected `jev-integration` environment, trusted manual or low-frequency
scheduled triggers, and no fork pull-request exposure.

For a step-by-step boundary analysis, environment examples, and failure behavior, read
[JEV credentials and subprocesses](jev-credentials-and-subprocesses.md).

## Artifacts and privacy

Full logs, traces, dumps, binlogs, SARIF, and JEV responses can contain private source,
paths, tokens, or user data even when terminal summaries are redacted. `.agent-tool/` and
generated `.agent-results/` content should stay ignored and local. Review and sanitize
before sharing; do not publish raw prompts, responses, transcripts, or private source.

JEV's heuristic secret detection is defense in depth, not a DLP system. Send the minimum
reviewed excerpt and route uncertainty back to Codex. See [JEV](../concepts/jev.md).

Public metrics have a separate disclosure boundary. Raw prompts, model responses,
transcripts, trial workspaces, private source, and local paths remain in ignored private
storage or short-lived CI artifacts. The publisher copies only manifest-approved,
schema-valid aggregates and rejects raw-field names, logs, secret-like content, and local
absolute paths. Human review remains required; schema validation cannot prove that an
innocent-looking aggregate is non-sensitive.

## Report a vulnerability

Do not open a public issue containing credentials, private source, dumps, or exploit
details. Use the product repository's private vulnerability reporting feature when
available; otherwise ask maintainers for a private channel before disclosing details.
The canonical supported-version policy is the product's
[`SECURITY.md`](https://github.com/simplexidev/sdeveng/blob/develop/v3.0.0/SECURITY.md).
