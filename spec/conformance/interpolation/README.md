<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# Interpolation — conformance vectors

These vectors define what resolving a `${…}` reference (spec §3.8) produces. A consumer that implements interpolation MUST produce every `out`; a consumer that emits one line per value MUST also produce every `lines`.

Interpolation never makes a document invalid — an address that does not resolve stays verbatim — so there are no negative vectors here: a malformed or unresolvable reference is a case whose `out` equals its `in`.

## Vector format

Each `*.yaml` validates against [`fixture.schema.json`](fixture.schema.json):

```yaml
description: <one line>
document: { … }        # a merged projectfile document every case resolves against
cases:
  - in: "<string>"     # an authored string value
    out: "<string>"    # what a consumer substituting into one string writes
    lines: [ … ]       # OPTIONAL: what a consumer writing one line per value emits
```

## Vectors

| File              | Covers                                                                                   |
| ----------------- | ---------------------------------------------------------------------------------------- |
| `references.yaml` | field addresses, list indexes, first-match selectors, verbatim non-scalars, `$$` escapes |
| `selectors.yaml`  | mapping selectors, multi-value references, per-value lines and their cross product       |
| `spans.yaml`      | repeat and optional spans, the `env` filter, unknown filters, a full command line        |
