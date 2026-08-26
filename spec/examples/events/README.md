<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# Events extension subtree fixtures

The fixtures in this directory are **not** whole projectfile documents and are
**not** validated against `spec/schema/v1.json` (which treats extension subtrees
as opaque per §3.7). Each file is the bare *value* of the
`org.projectfile.events` extension subtree, validated against that subtree’s
dedicated standalone schema:

| Fixture                        | Schema                                        | Expectation |
| ------------------------------ | --------------------------------------------- | ----------- |
| `org.projectfile.events.yaml`  | `../../schema/org.projectfile.events.v1.json` | MUST pass   |
| `negative/bad-url-string.yaml` | `../../schema/org.projectfile.events.v1.json` | MUST fail   |

The webhook target is an explicit runtime-env binding — `webhook.url: { env: NAME }`
(D6). The retired bare `${VAR}` string form (`negative/bad-url-string.yaml`) no
longer validates: a runtime-env reference must stay structurally distinct from a
generation-time `${<pf-path>}` projectfile reference, because a pf reference is an
in-document lookup resolved at generation time while a secret env is an
out-of-document forge value resolved at runtime (the invariant: in-document =>
`${…}`, out-of-document => a structured binding).
