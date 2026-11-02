# Storage boundaries and fit

Amplifier Data is a Python library with a native Rust storage kernel. It does
not run an agent or own a model. Optional intent compilers, interpreters and
embedders are supplied by consumers; their properties are not kernel guarantees.

## What the native contract provides

- Retain exact source bytes as content-addressed Cells in an append-only log.
- Derive scope membership, relationships and query views over the retained log.
- Reconstruct a Cell from its logged events and verify exact bytes after a
  materialized copy is dropped. An unlogged model output cannot be regenerated
  by replaying a model and assuming it will answer identically.
- Reopen durable logs through the compiled native kernel. The source-only Python
  fallback is in-memory and cannot establish restart durability.

## What scopes do not provide

A scope is a query-membership filter, **not an access-control list or tenant
security boundary**. Code with a store handle can query without a scope or read
another scope's Cell by reference. Consumers must enforce authorization and data
separation outside this API when required. A test of two filtered result sets
does not prove hostile-consumer isolation.

## Operational limits

- One writer owns a durable file. Use the supported companion server/RemoteStore
  seam for multiple processes; do not assume independent direct writers are safe.
- Invalidation is not erasure. Original bytes remain in the append-only history;
  review retention and privacy requirements before storing real material.
- Log growth, replay, derived indexes and optional interpretation have workload
  costs. Measure your record sizes, write/read pattern and recovery path. There
  is no universal latency or cost claim for every corpus.
- Native platform and toolchain acceptance are recorded separately. Do not infer
  Windows or another untested platform from a macOS/Ubuntu result.

## Determinism belongs to the selected layer

Exact logged payloads and pinned deterministic folds can be replayable. A caller
that adds a model-generated plan or interpretation must retain its defining
inputs, implementation/version and actual output rather than treating a seed or
a prompt as a byte-reproduction guarantee. Check native storage correctness
separately from the meaning or quality of an optional generated view.

## Compare the job before adding a substrate

For ordinary CRUD or an established relational application, compare the existing
SQLite/Postgres design first. This library is a candidate when exact retained
source evidence, derivation and replaceable consumer views solve a concrete job.
No superiority over those alternatives follows from the architecture alone.

Use a bounded fictional fixture first: positive scoped membership, a record that
must stay out of that query, exact regeneration and a fresh-process reopen. Then
measure an actual user's task, setup burden, recovery, time and cost against a
matched baseline. Unit tests do not establish outside-user usefulness.
