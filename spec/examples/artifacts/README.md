<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# Artifacts extension subtree fixtures

The fixtures in this directory are **not** whole projectfile documents and are
**not** validated against `spec/schema/v1.json` (which treats extension subtrees
as opaque per §3.7). Each file is the bare *value* of the
`org.projectfile.artifacts` extension subtree, validated against that subtree’s
dedicated standalone schema:

| Fixture                          | Schema                                           | Expectation |
| -------------------------------- | ------------------------------------------------ | ----------- |
| `org.projectfile.artifacts.yaml` | `../../schema/org.projectfile.artifacts.v1.json` | MUST pass   |
| `negative/bad-no-target.yaml`    | `../../schema/org.projectfile.artifacts.v1.json` | MUST fail   |

## Why a separate harness

The core schema (`v1.json`) treats every reverse-DNS extension subtree as opaque
and preserves it untouched — it never reaches inside `org.projectfile.artifacts`.
The per-namespace standalone schema is what pins the *shape* (named artifacts, a
`kind`, and at least one address field for that kind), so a producer, the CI tools
that consume `${org.projectfile.artifacts.<name>.path}`, and the documentation
generators that consume `${org.projectfile.artifacts{kind=image}.ref}` all agree
on the contract.
