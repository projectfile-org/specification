<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# projectfile/specification — agent guide

## Purpose

This repository defines **the projectfile specification**: a stack- and vendor-agnostic software project specification file. The deliverables in this session are spec-only — no reference implementation yet.

## Source of truth

- Normative specification: [`spec/v1.md`](spec/v1.md). If prose and schema disagree, prose wins and the schema MUST be updated to match.
- JSON Schema (machine-enforced): [`spec/schema/v1.json`](spec/schema/v1.json). Must agree with `spec/v1.md` at all times.
- Controlled vocabulary for `technologies:` field: [`spec/technologies.yaml`](spec/technologies.yaml).
- Non-normative registry of extension namespaces: [`spec/registry.yaml`](spec/registry.yaml). Illustrative shape fragments live in [`spec/shapes/`](spec/shapes/).

## Naming conventions

- The spec is called **"the projectfile specification"** or **"projectfile v1"**. Named after its file and its canonical domain `projectfile.org`.
- A file following the spec is a **projectfile document**, carried on disk as `projectfile.yaml` (RECOMMENDED) or `projectfile.json`. TOML remains a permitted encoding per `spec/v1.md`, but this repo’s own artifacts (dogfood, examples, shapes, registry) are authored in YAML/JSON only. The encoding is identified by the file extension; the document is the same.
- Future command-line tools will be named with whatever prefix the `sync` project decides; this repository holds no opinions on tool prefixes.

## Repository structure

```text
specification/
├── README.md                spec-repo overview
├── LICENSE                  MIT (code/schema)
├── LICENSES/CC-BY-4.0.txt   for spec text
├── CLAUDE.md / AGENTS.md    this guide
├── projectfile.yaml         self-dogfood (PRIMARY)
├── projectfile.json         self-dogfood (parity check)
├── IDEA.md                  pre-spec notes; preserved as-is
└── spec/
    ├── v1.md                NORMATIVE — the document
    ├── schema/v1.json       JSON Schema 2020-12
    ├── technologies.yaml     controlled vocabulary
    ├── registry.yaml        non-normative extension namespace registry
    └── examples/            pairs in .yaml / .json
        └── negative/        invalid fixtures (documented in negative/README.md)
```

## Rules for modifying the spec

- Additive, backward-compatible changes to v1 bump the revision date in the header of `spec/v1.md`. The schema `$id` and the `$schema:` URL in every document remain `https://projectfile.org/schema/v1.json`.
- Breaking changes spawn `spec/v2.md` and `spec/schema/v2.json`. v1 stays readable forever.
- Every reserved field added in a revision MUST have a corresponding `$defs` entry in the schema, at least one positive example (in both formats — YAML and JSON — where meaningful), and (if a plausible misuse exists) at least one negative fixture.
- **Un-reserving a field is NOT a breaking change and stays in v1.** The top-level `patternProperties: ^[a-z][a-z0-9-]*$` accepts any single-segment lowercase key with no constraint (§3.7 peaceful cohabitation), so dropping a field from `properties` and `$defs` leaves every existing document valid — the key simply becomes an unknown reserved key that consumers MUST preserve. `dependencies` (the former §4.9b) was removed this way: a projectfile can only mirror the DIRECT dependencies of one ecosystem, never a peer or a transitive, so it read as a complete manifest while being a lossy copy of the language-native manifest. Removing a field that documents still *use* would be breaking; removing one they may merely *carry* is not.
- The technology vocabulary (`spec/technologies.yaml`) grows by PR with a one-line rationale. No gate-keeping.

## Validation workflow

Since Node/npm is not available on the host, validate via Docker:

```sh
docker run --rm -v "$PWD:/w" -w /w python:3.14 sh -c '
  pip install --quiet check-jsonschema pyyaml &&
  # YAML and JSON validate directly
  check-jsonschema --schemafile spec/schema/v1.json spec/examples/*.yaml spec/examples/*.json
'
```

Every file under `spec/examples/` (except `negative/`) MUST pass validation. Every file under `spec/examples/negative/` MUST fail validation. `projectfile.{yaml,json}` at the repository root MUST pass.
