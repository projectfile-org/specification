<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# CI extension subtree fixtures

The fixtures in this directory are **not** whole projectfile documents and are
**not** validated against `spec/schema/v1.json` (which treats extension subtrees
as opaque per §3.7). Each file is the bare *value* of a CI extension subtree,
validated against that subtree’s dedicated standalone schema:

| Fixture                                      | Schema                                    | Expectation |
| -------------------------------------------- | ----------------------------------------- | ----------- |
| `org.projectfile.ci.yaml`                    | `../../schema/org.projectfile.ci.v1.json` | MUST pass   |
| `matrix.yaml`                                | `../../schema/org.projectfile.ci.v1.json` | MUST pass   |
| `matrix-overrides.yaml`                      | `../../schema/org.projectfile.ci.v1.json` | MUST pass   |
| `matrix-exclude.yaml`                        | `../../schema/org.projectfile.ci.v1.json` | MUST pass   |
| `matrix-serialise.yaml`                      | `../../schema/org.projectfile.ci.v1.json` | MUST pass   |
| `negative/bad-tool-override.yaml`            | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-node-key.yaml`                 | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-matrix-axis.yaml`              | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-matrix-overrides.yaml`         | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-matrix-exclude.yaml`           | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-manifest-field.yaml`           | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-action-image.yaml`             | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-secret-both-types.yaml`        | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-secret-docker-no-run.yaml`     | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-secret-in-env.yaml`            | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-secret-unknown-image-ref.yaml` | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-tool-inputs.yaml`              | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-when-event.yaml`               | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-dispatch-input.yaml`           | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-cron.yaml`                     | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-max-parallel.yaml`             | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-max-parallel-non-matrix.yaml`  | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-serialise-own-axes.yaml`       | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-action-slot.yaml`              | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-artifact-action.yaml`          | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-emit-event.yaml`               | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-mount-path.yaml`               | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-set-env-name.yaml`             | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-when-step.yaml`                | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |
| `negative/bad-advisory.yaml`                 | `../../schema/org.projectfile.ci.v1.json` | MUST fail   |

## Why a separate harness

A whole-document positive lives at `../ci-extensions.{yaml,json}` and validates
against `v1.json` — proving the subtree is accepted (opaque) by the core schema.
The dedicated `org.projectfile.ci.v1.json` schema enforces the subtree contract.

## Validate

```sh
docker run --rm -v "$PWD:/w" -w /w python:3.14 sh -c ‘
  pip install --quiet check-jsonschema pyyaml &&
  check-jsonschema --schemafile spec/schema/org.projectfile.ci.v1.json spec/examples/ci/org.projectfile.ci.yaml &&
  ! check-jsonschema --schemafile spec/schema/org.projectfile.ci.v1.json spec/examples/ci/negative/bad-tool-override.yaml &&
  ! check-jsonschema --schemafile spec/schema/org.projectfile.ci.v1.json spec/examples/ci/negative/bad-node-key.yaml &&
  ! check-jsonschema --schemafile spec/schema/org.projectfile.ci.v1.json spec/examples/ci/negative/bad-dispatch-input.yaml &&
  ! check-jsonschema --schemafile spec/schema/org.projectfile.ci.v1.json spec/examples/ci/negative/bad-cron.yaml
‘
```
