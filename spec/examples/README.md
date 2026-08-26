<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# Positive examples

Each file in this directory is a **valid** projectfile document under `spec/schema/v1.json`. Validators MUST accept every one. Examples are authored in YAML and JSON; the on-disk extension only identifies the encoding — the document is the same regardless.

The `Encodings` column lists which encodings exist for each example. The site at projectfile.org renders each example as a YAML / TOML / JSON tab widget; the TOML encoding is generated at build time from the YAML so it is never hand-maintained.

| Example             | Encodings  | Description                                                                                                                                                                               |
| ------------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `minimal`           | YAML, JSON | The smallest projectfile document that still validates: identity namespace, project name, license, and the v1 `$schema` discriminator — nothing else.                                     |
| `all-features`      | YAML, JSON | Exercises every reserved top-level key defined in v1. A reference for the full reserved-key set and a fixture validators MUST accept.                                                     |
| `realistic`         | YAML, JSON | A representative document for a typical mid-sized application — closer in shape to what a real-world project produces.                                                                    |
| `distribution`      | YAML, JSON | Distribution-channel metadata: registries, package coordinates, and OS-package targets.                                                                                                   |
| `cohabitation`      | YAML, JSON | Exercises the peaceful-cohabitation rule (§3.7): unknown reserved keys, bare-string localized values, and technology tags outside the recommended vocabulary are all preserved, not rejec |
| `unknown-extension` | YAML, JSON | Carries an extension namespace the validator has never seen — and that MUST validate, with the namespace preserved on round-trip.                                                         |
| `ci-extensions`     | YAML, JSON | A projectfile carrying the vendor-agnostic CI intent (`org.projectfile.ci`).                                                                                                              |
| `release`           | YAML, JSON | Release conventions (`org.projectfile.release`): tag format, changelog style, and the branch-to-channel map, with a `forge.kinds` hint for self-hosted forge resolution.                  |

## Encoding-specific examples

The `ci/` subdirectory carries standalone CI extension fragments (`org.projectfile.ci`) and their negative cases; see `ci/README.md`. Negative fixtures for the core schema live under `negative/` — see `negative/README.md`.
