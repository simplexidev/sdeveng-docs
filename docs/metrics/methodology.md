# Evaluation methodology

The evaluator asks whether a toolkit configuration improves task outcomes without paying
unnecessary cost. It does not assume that more skills, more agents, more delegation, or
fewer tokens are inherently better.

## Unit of comparison

An evaluation plan fixes the fixture, scenarios, repetitions, timeout, executor provider,
model, reasoning level, executable/version identity, judge configuration, and comparison
arms. Each arm and repetition runs in an isolated copy of the fixture. Selected skill and
agent overlays are copied into that workspace; candidates under the metrics repository are
evaluation inputs, not installed product features.

The executor and GPT judge must use OpenAI/GPT. Provider policy rejects non-OpenAI and
Claude identifiers. Bounded JEV may be used inside the judging hierarchy, but it is not an
evaluation executor and does not replace the GPT fallback.

## Comparison vocabulary

Evaluation records separate product comparison from agent customization:

| Field | Value | Meaning |
| --- | --- | --- |
| comparison | `VANILLA` | No compared toolkit/upstream optimization for the scenario. |
| comparison | `UPSTREAM` | The plan's pinned upstream skill overlay. This is not “all upstream content.” |
| comparison | `OPTIMIZED` | The toolkit configuration or new optimization named by the plan. |
| customization | `BUILTIN-or-NO-CUSTOM` | Native/built-in behavior without the custom agent being tested. |
| customization | `PREVIOUS-CUSTOM` | A historical custom-agent configuration retained only as a comparator. |
| customization | `OPTIMIZED-or-NEW` | The current or candidate custom-agent configuration named by the arm. |

The pre-optimization .NET baseline compares vanilla with a pinned upstream .NET skill
selection. Its agent baseline compares built-in/no-custom behavior with the current custom
reviewer. Later optimized arms must preserve compatible inputs or declare a new baseline;
labels alone do not make results comparable.

## Quality before efficiency

Evaluation stops at failed deterministic assertions or execution failure. Passing exact
checks may proceed to semantic judging. The complete quality surface includes:

- completion and exact deterministic assertions;
- safety assertions;
- semantic-rubric result;
- correct, false, and missed skill activation;
- correct, false, and missed delegation and tool invocation;
- nested-delegation expectations and context isolation;
- the final quality gate.

Efficiency metrics—tokens, turns, tool calls, context/files, elapsed time, subagents, and
full versus targeted build/test operations—are interpreted only after quality. A cheaper
arm that fails a required check is a quality regression, not an efficiency win.

## Metric evidence kinds

Every numeric run metric has a value, unit, evidence kind, and method:

| Kind | Interpretation |
| --- | --- |
| `measured` | Directly observed or counted by the named collection method. |
| `derived` | Calculated from measured/validated inputs, such as a mean, rate, or delta. |
| `estimated` | Approximated by a disclosed method; for example static tokens use `ceil(UTF-8 bytes / 4)`. |
| `unavailable` | Not faithfully obtainable. The value is `null` and the method explains why. |

Do not silently substitute zero for unavailable. Public aggregate schema v1 publishes
numeric measured, derived, or estimated values; unavailable run fields remain explicit in
private/validated evaluation records rather than becoming misleading dashboard numbers.

## Baseline compatibility and reuse

Raw trial reuse requires the exact compatibility hash and runner compatibility version.
The hash covers scenario expectations and prompts, fixture content, skill and agent
overlays, arm instructions, executor provider/model/reasoning/version/executable settings,
timeout, judge settings, and the runner compatibility version. Behavior-input directories
containing symbolic links are rejected.

If any behavior-affecting input changes, the old trial is stale and must run again. A
matching label, model family, or toolkit branch is insufficient. Aggregation also validates
records and rejects mixed toolkit revisions. Schema migrations are deterministic and may
not invent a measurement; an unfaithful value becomes unavailable.

## JEV and GPT judging

The judging order is:

1. execution success and deterministic assertions;
2. deterministic-only completion when configured;
3. an optional bounded JEV semantic rubric;
4. an OpenAI GPT judge when JEV is unavailable, invalid, or below confidence;
5. explicit failure for an invalid or failed GPT judgment.

The evaluator bounds the response supplied to a semantic judge. JEV use records calls,
fallbacks, escalations, confidence, and other routing outcomes separately from GPT judge
input/output/total tokens. Pairwise GPT evaluation runs both candidate orders; it reports a
winner only when normalized decisions agree. Consistent ties stay ties, while position-
sensitive or invalid outcomes are disagreements.

Calibration uses labeled examples sent to both configured judges. Live calibration is
explicit, private, capped at 50 examples, and not part of normal keyless CI. A suggested
confidence threshold needs at least three observations at 90% expected-outcome accuracy.
That small-sample rule prevents an unsupported recommendation but does not establish
general validity.

## Statistics and regression history

Evaluation records can contain repetitions, mean, median, minimum, maximum, and a named,
justified confidence interval. A one-repetition baseline is useful provenance but cannot
show run-to-run variance. Regression policy has per-metric direction, threshold, gate, and
minimum sample count. Comparisons below the minimum are labeled `insufficient-sample`
rather than passed through as strong evidence.

History retains the accepted baseline, toolkit/upstream/metrics revision lineage, gates,
deltas, and trends. Categories distinguish quality, tokens, tools, files/context,
validation breadth, routing, delegation, JEV, capability coverage, measurement noise, and
insufficient samples.

## Public sanitization

Raw prompts, JSONL events, responses, transcripts, private source, local paths, trial
records, and calibration details stay under ignored private storage or short-lived CI
artifacts. Aggregation emits only reviewed summaries conforming to the public schema.

Publication is a second gate: an explicit manifest allowlists JSON artifacts, and the
publisher rejects unlisted files, malformed aggregates, raw-field names, logs, secret-like
content, and local absolute paths. Public provenance states source-run count, sanitization,
and whether approval is synthetic or reviewed. Synthetic artifacts demonstrate the path;
claims based on real evaluations require reviewed approval. Schema checks do not replace
human disclosure review.

## Reproducibility checklist

When comparing two figures, verify the scenario set, arm definition, subject revision,
metrics revision, upstream revision where relevant, executor and judge identity, reasoning
level, repetitions, compatibility version/hash, metric kind/method/unit, cache state, and
provenance approval. If these differ, describe the comparison as directional or
incompatible instead of calculating a precise improvement.
