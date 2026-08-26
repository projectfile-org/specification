<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# build extension subtree fixtures

The fixtures in this directory are **not** whole projectfile documents and are
**not** validated against `spec/schema/v1.json` (which treats extension subtrees
as opaque per §3.7). Each file is the bare *value* of an `org.projectfile.build`
subtree, validated against that subtree's dedicated standalone schema:

| Fixture                      | Schema                                       | Expectation |
| ---------------------------- | -------------------------------------------- | ----------- |
| `org.projectfile.build.yaml` | `../../schema/org.projectfile.build.v1.json` | MUST pass   |
| `org.projectfile.build.json` | `../../schema/org.projectfile.build.v1.json` | MUST pass   |
| `negative/bad-arg-keys.yaml` | `../../schema/org.projectfile.build.v1.json` | MUST fail   |

The positive fixture exercises every block — `registries`, every `args` value
rule (a literal, an `@base` version sentinel, an empty forward-if-set, a bare
scalar coerced to its literal string, and a `file` read), and `env`. Tool-run
images live under `org.projectfile.ci.images`, not here. The negative fixture
pairs `default` and `file` on one arg, which the `$defs.arg` `oneOf` forbids.

## Validate

```sh
docker run --rm -v "$PWD:/w" -w /w python:3.14 sh -c '
  pip install --quiet check-jsonschema pyyaml &&
  check-jsonschema --schemafile spec/schema/org.projectfile.build.v1.json spec/examples/build/org.projectfile.build.yaml &&
  check-jsonschema --schemafile spec/schema/org.projectfile.build.v1.json spec/examples/build/org.projectfile.build.json &&
  ! check-jsonschema --schemafile spec/schema/org.projectfile.build.v1.json spec/examples/build/negative/bad-arg-keys.yaml
'
```
