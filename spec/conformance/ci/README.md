<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# org.projectfile.ci — conformance vectors

These vectors define what it **means** to consume the CI-DAG extension
correctly, independent of any one vendor. They are the executable definition of
the extension’s behaviour: every conforming consumer (m6e → make, t4n → Tekton,
GitHub/Forgejo workflow generators) MUST produce the stated outcome.

## Schema validation vs conformance

Two different contracts, deliberately kept apart:

| Layer         | Asks                                       | Lives in                                                      | Checked by                  |
| ------------- | ------------------------------------------ | ------------------------------------------------------------- | --------------------------- |
| **Structure** | Is this well-formed CI intent?             | `spec/examples/ci/`, `spec/schema/org.projectfile.ci.v1.json` | JSON Schema                 |
| **Behaviour** | Does resolving it produce the right graph? | **here**                                                      | a resolver runs each vector |

JSON Schema cannot express closure, ordering, or cycles — so a cyclic graph is
*schema-valid* yet *non-conformant*. That gap is exactly what these vectors fill.

## Vector format

Each `*.yaml` validates against [`fixture.schema.json`](fixture.schema.json):

```yaml
description: <one line>
includes:            # OPTIONAL ordered include chain; each entry is a CI subtree.
  - { nodes: { … } } #   Deep-merged in order, then `input` on top (base wins, §4.9a).
input:               # REQUIRED: the base document's org.projectfile.ci subtree.
  nodes: { … }       #   a node may carry `goal: true` to mark a default target.
expect:              # REQUIRED: accept mode (runs/counts/before/args) XOR reject mode.
  runs: [ … ]
```

`input` (and every `includes` entry) MUST also satisfy
`spec/schema/org.projectfile.ci.v1.json` — **except** the reject-mode semantic
defect (a `needs` cycle), which is structurally valid and caught only by a
resolver.

## Assertion vocabulary

A vector is in **accept** mode (the graph resolves) or **reject** mode (a
resolver must refuse). The two are mutually exclusive.

### Accept mode

The goals evaluated are the nodes flagged `goal: true`, or — when none is
flagged — every **sink** (a node no other node `needs`). The work that runs is
the transitive `needs`-closure of those goals.

| Key                 | Meaning                                                                                                                                                                                                                        |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `runs` (required)   | The **exact** set of tool names that execute. Closed world: any tool not listed MUST NOT run. With a `matrix`, the **distinct** set; per-cell multiplicity is `counts`.                                                        |
| `counts` (optional) | `tool: <int>` execution multiplicity (default 1 each). A matrix **CELL** tool runs once per product cell left after `matrix.exclude`, a **SOURCE** tool once before the cells, a **JOIN** tool once after every cell — `counts` pins that fan-out/fan-in.  |
| `before` (optional) | Ordering pairs `[X, Y]`: tool `X` completes strictly before tool `Y` starts. The observable guarantee only — how a consumer lowers it (make order-only prerequisite, Tekton `runAfter`, workflow `needs`) is its own business. |
| `args` (optional)   | `tool: "<string>"` the consumer must deliver to that tool’s invocation (the `{args: "…"}` override branch). A consumer’s own CLI override may still win at runtime; this pins the resolved default.                            |

`runs` asserts over **tools**, not nodes, because tools are the only things that
execute — a pure-join node (`published` with no `tools`) contributes nothing to
`runs`. Tool names are treated as one logical execution each.

### Reject mode

| `reject` value | The defect the resolver MUST refuse                      |
| -------------- | -------------------------------------------------------- |
| `cycle`        | `needs` edges form a loop — no topological order exists. |

(A `needs` entry naming an undeclared node is **not** a defect — it is treated
as a tool target; see `reject-dangling-need.yaml`, an accept-mode vector. With
`goal` now a per-node flag, an unknown-goal reference is structurally
impossible.)

## The conformance contract

A consumer **passes** a vector iff:

- **accept** — the set of tools it executes for the goals equals `runs`; every
    `counts` multiplicity (the matrix fan-out/fan-in) holds; every `before` ordering holds; every `args` mapping is delivered.
- **reject** — it refuses to build the graph, for the stated reason, before running any tool.

## Vectors

| Vector                        | Pins                                                                                       |
| ----------------------------- | ------------------------------------------------------------------------------------------ |
| `closure-goal-exclusion.yaml` | a `goal: true` node runs only its closure; off-closure sink excluded                       |
| `ordering-after-build.yaml`   | `needs` orders a validator strictly after the build tool                                   |
| `sink-inference.yaml`         | no `goal` flag ⇒ every sink is a goal                                                      |
| `include-false-override.yaml` | local `false` beats an included `true` (§4.9a)                                             |
| `args-passthrough.yaml`       | `{args}` enables a tool and binds its arg string                                           |
| `scalar-need.yaml`            | scalar `needs: <target>` shorthand == the one-entry sequence; a scalar naming a node is an |
| `matrix-percell.yaml`         | a `matrix: true` CELL fans out across the axes’ product; an upstream SOURCE tool runs once |
| `matrix-join.yaml`            | a non-flagged node downstream of a CELL is a JOIN — runs once, after every cell            |
| `matrix-exclude.yaml`         | `matrix.exclude` subtracts cells — a CELL fans over the product MINUS the excluded ones    |
| `reject-cycle.yaml`           | cyclic `needs` rejected                                                                    |
| `reject-dangling-need.yaml`   | `needs` to an undeclared node is treated as a tool (accept-mode)                           |

## Validate the vectors themselves

This checks the vector *files* are well-formed (not that a resolver obeys them —
that is each consumer’s own test run):

```sh
docker run --rm -v "$PWD:/w" -w /w python:3.14 sh -c '
  pip install --quiet check-jsonschema pyyaml &&
  check-jsonschema --schemafile spec/conformance/ci/fixture.schema.json \
    spec/conformance/ci/*.yaml
'
```
