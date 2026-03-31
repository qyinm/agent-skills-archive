---
name: experiment-loop
description: Autonomous optimization loop. Modifies code, measures a target metric, keeps improvements, and reverts regressions. Use for API latency, OCR time, bundle size, query speed, or any measurable optimization task.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Overview

This skill turns the autoresearch idea into a general-purpose optimization workflow:

1. define a measurable target
2. establish a baseline
3. make one controlled change
4. measure again
5. keep wins, discard regressions
6. repeat until the goal is reached or the user stops the loop

Use this when the work can be judged by a stable numeric metric, such as:

- API response latency
- OCR processing time
- batch job runtime
- memory usage
- bundle size
- query execution time
- throughput or success rate

# Inputs To Lock Down First

Before changing code, make these explicit:

1. **Target metric**
   - Example: p95 latency, average OCR runtime, bundle size in KB, requests/sec
2. **Direction**
   - `lower` is better for latency, runtime, memory, error count
   - `higher` is better for throughput, accuracy, cache hit rate
3. **Target or stopping condition**
   - Example: `p95 < 200ms`, `OCR < 1.0s`, `bundle < 500KB`
4. **Measurement command**
   - A repeatable command or script that prints the metric in a machine-readable way
5. **Timeout**
   - Hard limit for one experiment run to prevent hangs

If any of these are missing, define them before beginning the loop.

# Measurement Contract

The optimization loop is only as good as the measurement.

The metric command must be:

- repeatable
- cheap enough to run many times
- stable enough that noise does not dominate decisions
- printed in a parseable format

Use this output contract:

```text
metric_name: <name>
metric_value: <number>
metric_unit: <unit>
```

Example outputs:

```text
metric_name: api_p95_ms
metric_value: 184.2
metric_unit: ms
```

```text
metric_name: ocr_runtime_seconds
metric_value: 0.93
metric_unit: s
```

If the repository has no benchmark or profiler yet, create the smallest reliable measurement path first. Prefer a simple project-local script over complicated tooling.

# Branching And State

Run the loop on an isolated branch:

1. propose a run tag, usually date-based or goal-based
2. create `experiment/<tag>` from the current working branch
3. keep each experiment in its own commit
4. log every attempt in `results.tsv`

Example branch names:

- `experiment/api-latency-mar31`
- `experiment/ocr-sub1s-apr1`
- `experiment/query-speed-indexes`

# results.tsv Format

Create a local `results.tsv` file for the run and do not rely on memory.

Use tab-separated columns:

```text
commit	metric_name	metric_value	unit	status	description
```

Statuses:

- `keep`: better than baseline or current best
- `discard`: valid run but no improvement
- `crash`: benchmark or app failed

Example:

```text
commit	metric_name	metric_value	unit	status	description
a1b2c3d	api_p95_ms	248.5	ms	keep	baseline
b2c3d4e	api_p95_ms	211.7	ms	keep	cache parsed auth headers
c3d4e5f	api_p95_ms	230.1	ms	discard	increase worker count
d4e5f6g	api_p95_ms	0	ms	crash	memoize request body parser
```

# Setup Workflow

When invoked:

1. Read the relevant implementation and existing benchmark path.
2. Identify which files are in scope for optimization.
3. Create or verify a benchmark/measurement command.
4. Create the experiment branch.
5. Initialize `results.tsv` from the template in `templates/results.tsv` if missing.
6. Run the baseline measurement first with no optimization changes.
7. Log the baseline before attempting any edits.

# Optimization Loop

Repeat until the target is reached or the user interrupts you:

1. Check current git state and the current best metric.
2. Form one concrete optimization hypothesis.
3. Change the smallest relevant code path.
4. Commit the experiment.
5. Run the measurement command and capture all output to a log file.
6. Parse the metric result.
7. Record the run in `results.tsv`.
8. Compare with the current best:
   - if improved, keep the commit and continue from there
   - if worse or equal, revert to the previous best commit
9. Repeat with the next idea.

Prefer single-variable experiments. Avoid mixing many changes in one attempt unless they are tightly coupled.

# Decision Rules

## For `lower` metrics

Keep the change if the new metric is lower than the current best.

## For `higher` metrics

Keep the change if the new metric is higher than the current best.

## Noise handling

If results are noisy or close:

- rerun the same benchmark once or twice
- prefer median or p95 over a single raw sample
- do not keep tiny regressions just because they are within noise

## Simplicity bias

If two versions are effectively tied, keep the simpler one.

# Benchmark Execution Rules

- Redirect experiment output to a log file such as `run.log`.
- Do not flood the assistant context with full benchmark output.
- Parse only the fields needed for the decision.
- If a run exceeds the agreed timeout, stop it and mark it as `crash`.
- If the run crashes due to an obvious bug introduced by the current experiment, fix once and rerun.
- If the idea itself is bad or unstable, discard it and move on.

# What To Optimize

Good categories of changes include:

- algorithmic improvements
- query/index changes
- batching and caching
- I/O reduction
- removing redundant work
- reducing serialization/deserialization overhead
- concurrency tuning
- avoiding repeated allocations
- replacing hot-path abstractions with simpler code

Avoid broad refactors unless they directly support the metric.

# Examples

## API latency

Goal:

- metric: `api_p95_ms`
- direction: `lower`
- target: `< 200ms`

Possible measurement command:

```bash
./scripts/benchmark-api.sh
```

Possible ideas:

- cache auth verification results
- reduce N+1 queries
- precompute response payloads
- move JSON parsing out of the hot path

## OCR runtime

Goal:

- metric: `ocr_runtime_seconds`
- direction: `lower`
- target: `< 1.0s`

Possible ideas:

- reduce image preprocessing passes
- batch model calls differently
- resize earlier
- skip redundant postprocessing
- move heavyweight initialization out of per-request flow

# Guardrails

- Do not change the benchmark definition mid-run unless the user agrees.
- Do not declare success without recording the measured result.
- Do not keep regressions because they "feel" cleaner.
- Do not stop after one improvement if the target is still far away.
- Do not silently ignore crashes; log them.

# Output Back To The User

When reporting progress, include:

1. current best metric
2. baseline metric
3. latest experiment status
4. what changed
5. whether the target has been reached

When the loop ends, summarize:

- best metric achieved
- total experiments attempted
- changes that produced the biggest gains
- remaining bottlenecks or next hypotheses
