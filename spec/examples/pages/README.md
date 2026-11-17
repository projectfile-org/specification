<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>

SPDX-License-Identifier: CC-BY-4.0
SPDX-License-Identifier: MIT
-->

# Pages extension subtree fixtures

Each file is the bare *value* of the `org.projectfile.pages` extension subtree,
validated against `../../schema/org.projectfile.pages.v1.json`. They are not whole
projectfile documents, and `spec/schema/v1.json` treats the subtree as opaque (§3.7).

`org.projectfile.pages.yaml` MUST pass. Every file under `negative/` MUST fail, each
on one edge:

| Fixture                       | Edge                                           |
| ----------------------------- | ---------------------------------------------- |
| `uppercase-name.yaml`         | a collection name is lowercase                 |
| `digit-first-name.yaml`       | a collection name starts with a letter         |
| `leading-hyphen-name.yaml`    | a collection name starts with a letter         |
| `trailing-hyphen-name.yaml`   | a collection name ends with a letter or digit  |
| `double-hyphen-name.yaml`     | words are joined by one hyphen                 |
| `non-object-collection.yaml`  | a collection is a mapping                      |
| `unknown-key.yaml`            | a collection carries `title` and `command` only |
| `missing-title.yaml`          | `title` is required                            |
| `empty-title.yaml`            | a bare title is never empty                    |
| `empty-title-map.yaml`        | a title map names at least one language        |
| `empty-localized-title.yaml`  | no language in a title map is empty            |
| `bad-language-tag.yaml`       | a title map key is a BCP 47 tag                |
| `string-command.yaml`         | `command` is an argv list, never a shell string |
| `empty-command.yaml`          | `command` names at least the executable        |
| `empty-argument.yaml`         | no argument in `command` is empty              |

The positive fixture pins the other side of each edge: the one-letter name `x`, a
digit after the first hyphen (`output-formats-2`), a bare-string title, the `und`
and region-subtagged (`pt-BR`) title keys, and a one-element `command`.
