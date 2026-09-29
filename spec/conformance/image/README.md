<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# org.projectfile.image — tag naming and conformance vectors

Every name an image carries is declared by its document as a template; this
page fixes the mechanism that expands them, never a naming convention. A
project may keep a build variant (a series, a SAPI, a libc) in its `path`
(`b19/ubuntu/resolute`) or move it into the tag (`b19/ubuntu:resolute`) — both
are declarations, and both are conformant. The vectors below are the
executable form of this page.

## Mechanism

- **Build facts** — what a publisher knows about the build: `${version}` (a
  leading `v` removed) and, when it is `MAJOR.MINOR.PATCH`, `${major}`,
  `${minor}`, `${patch}`; on a branch push, `${branch}` with every byte outside
  `[A-Za-z0-9._-]` replaced by `-`.
- **Kind** — `trunk` for a push to a primary branch, `branch` for any other
  branch, `release` for a `MAJOR.MINOR.PATCH` version, `prerelease` for any
  other version.
- **Heads** — the templates `heads.<kind>` declares, expanded over the build
  facts. A kind the document leaves undeclared publishes the publisher’s
  default.
- **Part value** — a part template with every `{AXIS}` replaced by the matrix
  cell’s value; its **default** is the same template with every `{AXIS}`
  replaced by that axis’ `org.projectfile.build.args` default. A part that
  names an axis with no default has no default.
- **Cell** — one distinct combination of the `variant.parts` values across the
  `org.projectfile.ci.matrix` cells left after `matrix.exclude`. Matrix cells
  that differ only in an axis no variant part names (the architecture) are ONE
  cell and publish one manifest list. No matrix is one cell.
- **Variants of a cell** — the part values joined with `variant.join`, in
  `variant.parts` order. With `aliases: defaults`, also every combination with
  default-valued parts dropped, order kept, down to the empty variant.
- **Tags of a cell** — for every head × every variant: the head alone when the
  variant is empty; the variant alone when the head is listed in
  `variant.bare`; `variant.tag` expanded over `${head}` and `${variant}`
  otherwise. Without `variant`, the tags are the heads.
- **Refusal** — a publisher MUST refuse, before pushing anything, when
  - `cell-collision`: two cells derive a common tag, or
  - `head-is-variant`: a head equals a non-empty variant of any cell, since the
    head moves with the next build and the variant must not.

## Vector format

Each `*.yaml` validates against [`fixture.schema.json`](fixture.schema.json):

```yaml
description: <one line>
input:                              # the subtrees the mechanism reads
  image: { heads: …, variant: … }   #   org.projectfile.image
  build: { args: { … } }            #   org.projectfile.build (defaults)
  ci: { matrix: { … } }             #   org.projectfile.ci (cells)
publish: { version: 0.3.0 }         # the build facts: `version`, or `branch` + `trunk`
expect:                             # accept mode XOR reject mode
  cells:
    - cell: { part: value }
      tags: [ … ]                   # the EXACT tag set of that cell
```

A publisher **passes** a vector iff, in accept mode, it derives exactly the
listed tag set for every listed cell and derives no other cell; in reject mode
it refuses for the stated reason. The rules each vector declares are sample
data, not a recommendation.

## Vectors

| Vector                        | Pins                                                              |
| ----------------------------- | ----------------------------------------------------------------- |
| `one-axis.yaml`               | one part; aliases give the default series the bare heads          |
| `two-axes.yaml`               | two parts; every combination of default values is dropped         |
| `non-numeric-default.yaml`    | a non-numeric default aliases; the arch axis splits no cell       |
| `fixed-variant.yaml`          | a part naming no matrix axis takes its default                    |
| `branch-head.yaml`            | a branch build expands its own head templates                     |
| `other-rules.yaml`            | variant first, another separator, no aliases, no bare heads       |
| `no-variant.yaml`             | no `variant` publishes the heads unchanged                        |
| `reject-cell-collision.yaml`  | two cells deriving one tag are refused                            |
| `reject-head-is-variant.yaml` | a head spelled like a variant is refused                          |

## Validate the vectors themselves

```sh
docker run --rm -v "$PWD:/w" -w /w python:3.14 sh -c '
  pip install --quiet check-jsonschema pyyaml &&
  check-jsonschema --schemafile spec/conformance/image/fixture.schema.json \
    spec/conformance/image/*.yaml
'
```
