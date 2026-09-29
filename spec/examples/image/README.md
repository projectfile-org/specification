<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# image extension subtree fixtures

Each file is the bare *value* of an `org.projectfile.image` subtree, validated
against its standalone schema, not against `spec/schema/v1.json`.

| Fixture                        | Schema                                       | Expectation |
| ------------------------------ | -------------------------------------------- | ----------- |
| `org.projectfile.image.yaml`   | `../../schema/org.projectfile.image.v1.json` | MUST pass   |
| `org.projectfile.image.json`   | `../../schema/org.projectfile.image.v1.json` | MUST pass   |
| `negative/scalar-variant.yaml` | `../../schema/org.projectfile.image.v1.json` | MUST fail   |

The tag a publish derives from `variant` is behaviour, not structure: see
[`../../conformance/image/`](../../conformance/image/README.md).

## Validate

```sh
docker run --rm -v "$PWD:/w" -w /w python:3.14 sh -c '
  pip install --quiet check-jsonschema pyyaml &&
  check-jsonschema --schemafile spec/schema/org.projectfile.image.v1.json spec/examples/image/org.projectfile.image.yaml spec/examples/image/org.projectfile.image.json &&
  ! check-jsonschema --schemafile spec/schema/org.projectfile.image.v1.json spec/examples/image/negative/scalar-variant.yaml
'
```
