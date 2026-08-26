<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# Negative fixtures

Each file in this directory is **invalid** under `spec/schema/v1.json`. Validators SHOULD reject each with a clear error pointing at the offending path.

Negative fixtures in this directory are authored in YAML and JSON. Some breakages are encoding-specific (e.g. the TOML `spec_version` discriminator, or TOML rejecting duplicate keys at parse time); those are documented in prose here and in `spec/v1.md` rather than carried as fixtures, since this repo’s artifacts are YAML/JSON only. The `Encodings` column lists which fixture files exist for each case.

| Fixture                     | Encodings  | Why it fails                                                                                                                                                                                                                                                                                                       |
| --------------------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `no-discriminator`          | YAML, JSON | Missing the YAML/JSON `$schema` discriminator (Section 3.4). The same breakage in TOML manifests as a missing `spec_version`.                                                                                                                                                                                      |
| `wrong-schema-url`          | YAML, JSON | `$schema:` value does not match the v1 canonical URL. The TOML analogue is a `spec_version` other than `"1"`.                                                                                                                                                                                                      |
| `uppercase-key`             | YAML, JSON | `Identity` is uppercase; top-level keys MUST match `^[a-z][a-z0-9-]*$` (Section 3.6).                                                                                                                                                                                                                              |
| `digit-first-extension`     | YAML, JSON | Top-level key `1.2.3` has a digit-first first label and is rejected by `additionalProperties:false` plus the patternProperties shape. (TOML cannot express this case as a single literal top-level key — `[1.2.3]` parses as nesting and `"1.2.3" = ...` is permitted but creates a key matching neither pattern.) |
| `dotted-string-key`         | YAML, JSON | Dotted `"dev.acme.tool":` form (single literal dotted key) is not a valid extension namespace under v1; extension namespaces MUST be nested mappings (Section 3.6).                                                                                                                                                |
| `two-origin-repositories`   | YAML, JSON | Two entries in `repositories[]` both carry `role: origin`. At most one entry may be origin (§4.3a); schema enforces this with `contains` + `maxContains: 1`.                                                                                                                                                       |
| `missing-origin-repository` | YAML, JSON | `repositories[]` has two entries and neither carries `role: origin`. When the array has more than one entry, exactly one MUST be origin (§4.3a); schema enforces with `if minItems: 2 then minContains: 1`.                                                                                                        |
| `malformed-person-from`     | YAML, JSON | A `people[]` entry’s `from` is `"2025/09/12"` (slash-separated). Per-person `from` / `to` MUST be ISO-8601 `YYYY-MM-DD`; the schema’s `isoDate` pattern rejects the slashed form.                                                                                                                                  |
| `absolute-license-file`     | YAML, JSON | `license.file` is an absolute path; path traversal prohibited (Section 8).                                                                                                                                                                                                                                         |
| `people-mixed-shape`        | YAML, JSON | A `people[]` entry carries `name`, which is forbidden in persons — entities go in `organizations[]`. The schema rejects `name` as an additional property on person entries (Section 4.5).                                                                                                                          |

## Note on cohabitation (Section 3.7)

Several behaviors that earlier drafts treated as errors are now **accepted** under the peaceful-cohabitation rule:

- **Unknown reserved keys** (single-segment lowercase keys not defined in this version of Section 4) — accepted and preserved on round-trip. See `../cohabitation.{yaml,json}`.
- **Bare string in a localized-string position** — accepted as shorthand for `{ und: "<string>" }`. See `../cohabitation.{yaml,json}`.
- **Technology tags outside the recommended vocabulary** — accepted; the vocabulary is non-normative. See `../cohabitation.{yaml,json}`.

## Positive contrast

`../unknown-extension.{yaml,json}` carries an extension namespace the validator has never seen — and that MUST validate. `../cohabitation.{yaml,json}` exercises the broader cohabitation contract.
