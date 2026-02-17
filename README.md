# Practice Desk

[![CI](https://github.com/joserprieto/practice-desk/actions/workflows/ci.yml/badge.svg)](https://github.com/joserprieto/practice-desk/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Professional practice management platform** — document collection, appointments, and client
operations for service firms.

**[Roadmap](ROADMAP.md)** | **[Contributing](CONTRIBUTING.md)** | **[Changelog](CHANGELOG.md)**

## Overview

Practice Desk helps professional service firms (law offices, consultancies, accounting firms) manage
their day-to-day operations: client intake, document collection, appointment scheduling, case
tracking, and deadline management.

## Status

**Pre-development** — project infrastructure and analysis phase. No application code yet.

## Quick Start

```bash
# Clone
git clone https://github.com/joserprieto/practice-desk.git
cd practice-desk

# Install tooling (Node.js required for linting and releases)
npm install

# Verify setup
make check/deps
```

## Commands

| Command                | Description                         |
| ---------------------- | ----------------------------------- |
| `make help`            | Show all available commands         |
| `make check/deps`      | Verify dependencies                 |
| `make lint`            | Run all linters (markdown, shell)   |
| `make lint/fix`        | Auto-fix lint issues where possible |
| `make lint/md`         | Lint markdown files                 |
| `make lint/md/fix`     | Fix markdown lint issues            |
| `make lint/shell`      | Lint shell scripts (shellcheck)     |
| `make format`          | Format markdown and config files    |
| `make format/check`    | Check formatting without changes    |
| `make release/dry-run` | Preview release                     |
| `make release/patch`   | Create patch release                |
| `make release/minor`   | Create minor release                |
| `make release/major`   | Create major release                |

## Project Structure

```
practice-desk/
├── .github/                # CI/CD workflows, issue templates, automation scripts
│   ├── workflows/          # GitHub Actions (CI, label sync)
│   ├── scripts/            # CI automation (issue create/close on failure/success)
│   └── config/             # Labels configuration
├── .changelog-templates/   # Handlebars templates for auto-generated changelog
├── Makefile                # Project orchestration
├── README.md               # This file
├── CHANGELOG.md            # Auto-generated from conventional commits
├── ROADMAP.md              # Planned features and milestones
├── CONTRIBUTING.md         # Contribution guidelines
├── SECURITY.md             # Security policy
├── CODE_OF_CONDUCT.md      # Community standards
└── LICENSE                 # MIT License
```

## Development

### Prerequisites

- **Node.js 20+** — For linting and release tooling
- **shellcheck** — For shell script linting (`brew install shellcheck`)
- **GNU Make** — Build orchestration

### Code Quality

All contributions must pass:

- **Markdown lint** — [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2) with
  auto-fix
- **Shell lint** — [shellcheck](https://www.shellcheck.net/) for all `.sh` scripts
- **Prettier** — Formatting for markdown, JSON, YAML
- **Conventional Commits** — Enforced via [gitlint](https://jorisroovers.com/gitlint/)

### Commit Convention

We use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `ci`, `perf`, `build`, `revert`

## Contributing

Contributions welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting PRs.

## License

MIT License — see [LICENSE](LICENSE) for details.
