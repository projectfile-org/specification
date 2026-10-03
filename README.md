<!--
SPDX-FileCopyrightText: 2026 Damián Búho <damian.buho@proton.me>
SPDX-License-Identifier: MIT OR CC-BY-4.0
pf-cli-managed: yes
-->

[Español](docs/es/README.md) · [Українська](docs/uk/README.md)

# The Projectfile Specification

The normative specification of the projectfile: a single stack- and vendor-agnostic YAML file that describes a software project identity, build, CI and publishing. Holds the spec prose and v1 JSON Schema, dual-licensed MIT and CC-BY-4.0, rendered at projectfile.org and consumed by the pf-cli, pf-bridge and pf-ci tools.

[![Stand with Ukraine](https://raw.githubusercontent.com/vshymanskyy/StandWithUkraine/main/badges/StandWithUkraine.svg)](https://damian-buho.github.io/support-ukraine/) [![Projectfile inside](https://badges.kiota.ch/static/v1?label=projectfile&message=inside&labelColor=0d0d0d&color=8c6723&style=flat-square)](https://projectfile.org) [![License](https://badges.kiota.ch/static/v1?label=license&message=MIT%20OR%20CC-BY-4.0&color=1e5913&style=flat-square)](LICENSE) [![PRs welcome](https://badges.kiota.ch/static/v1?label=PRs&message=welcome&color=1e5913&style=flat-square)](CONTRIBUTING.md) [![REUSE compliance](https://api.reuse.software/badge/codeberg.org/projectfile/specification)](https://api.reuse.software/info/codeberg.org/projectfile/specification)

![Project status](https://badges.kiota.ch/static/v1?label=status&message=maintained&color=1d63ed&style=flat-square) [![Last commit on kiota.ch](https://badges.kiota.ch/gitea/last-commit/projectfile/specification?gitea_url=https://kiota.ch&label=last%20commit%20on%20kiota.ch&style=flat-square)](https://kiota.ch/projectfile/specification) [![Last commit on Codeberg](https://badges.kiota.ch/gitea/last-commit/projectfile/specification?gitea_url=https://codeberg.org&label=last%20commit%20on%20Codeberg&style=flat-square)](https://codeberg.org/projectfile/specification) [![Last commit on GitHub](https://badges.kiota.ch/github/last-commit/projectfile-org/specification?label=last%20commit%20on%20GitHub&style=flat-square)](https://github.com/projectfile-org/specification)

[![Publish pipeline on GitHub](https://github.com/projectfile-org/specification/actions/workflows/published.yaml/badge.svg?style=flat-square)](https://github.com/projectfile-org/specification/actions) [![Vulnerability audit on GitHub](https://github.com/projectfile-org/specification/actions/workflows/audited.yaml/badge.svg?style=flat-square)](https://github.com/projectfile-org/specification/actions) [![Dependency freshness on GitHub](https://github.com/projectfile-org/specification/actions/workflows/check-outdated.yaml/badge.svg?style=flat-square)](https://github.com/projectfile-org/specification/actions) [![Analysis sweep on GitHub](https://github.com/projectfile-org/specification/actions/workflows/analyzed.yaml/badge.svg?style=flat-square)](https://github.com/projectfile-org/specification/actions)

[![Publish pipeline on kiota.ch](https://kiota.ch/projectfile/specification/badges/workflows/published.yaml/badge.svg?style=flat-square)](https://kiota.ch/projectfile/specification/actions) [![Vulnerability audit on kiota.ch](https://kiota.ch/projectfile/specification/badges/workflows/audited.yaml/badge.svg?style=flat-square)](https://kiota.ch/projectfile/specification/actions) [![Dependency freshness on kiota.ch](https://kiota.ch/projectfile/specification/badges/workflows/check-outdated.yaml/badge.svg?style=flat-square)](https://kiota.ch/projectfile/specification/actions) [![Analysis sweep on kiota.ch](https://kiota.ch/projectfile/specification/badges/workflows/analyze.yaml/badge.svg?style=flat-square)](https://kiota.ch/projectfile/specification/actions)

## Building

Clone the repository with its submodules:

```sh
git clone --recurse-submodules https://codeberg.org/projectfile/specification specification && cd specification
```

- [Makefile reference](docs/how-to/MAKEFILE.md)

Run `make` with no arguments for the default target; run `make help` to list every target.

Pipeline entry points:

- `make analyzed` — Run the heavy analysis sweep (mutation testing, benchmarks)
- `make audited` — Re-scan the pinned dependencies and published artifacts for new vulnerabilities
- `make check-outdated` — Report every pinned dependency that lags upstream
- `make ready-to-publish` — Run the pseudo-CI pipeline locally — build, test and scan, without publishing

## Policies

- [How to contribute](CONTRIBUTING.md)
- [Security policy](SECURITY.md)
- [Getting support](SUPPORT.md)
- [Code of Conduct](CODE_OF_CONDUCT.md)
- [AI and LLM Policy](AI_POLICY.md)

## Links

- [Projectfile Specification](https://projectfile.org)
- [Read projectfile v1](https://projectfile.org/spec/v1/)

## License

This project is licensed under MIT OR CC-BY-4.0 — see the [LICENSE](LICENSE) file for details.
