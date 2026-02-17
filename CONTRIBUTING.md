# Contributing to Practice Desk

Thank you for your interest in contributing! This guide will help you get started.

## Prerequisites

- **Node.js 20+** — Required for linting and releases
- **shellcheck** — Shell script linting (`brew install shellcheck`)
- **GNU Make** — Build orchestration

## Development Setup

```bash
# Clone and install
git clone https://github.com/joserprieto/practice-desk.git
cd practice-desk
npm install

# Verify setup
make check/deps
```

## Development Workflow

1. Create a feature branch from `main`
2. Make your changes
3. Run `make lint` to check for issues
4. Run `make lint/fix` to auto-fix where possible
5. Commit using Conventional Commits
6. Open a Pull Request

## Commit Convention

We use [Conventional Commits](https://www.conventionalcommits.org/):

```text
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type       | Description                             |
| ---------- | --------------------------------------- |
| `feat`     | New feature                             |
| `fix`      | Bug fix                                 |
| `docs`     | Documentation only                      |
| `style`    | Formatting, no code change              |
| `refactor` | Code change that neither fixes nor adds |
| `perf`     | Performance improvement                 |
| `test`     | Adding or correcting tests              |
| `build`    | Build system or dependencies            |
| `ci`       | CI configuration                        |
| `chore`    | Other changes (maintenance)             |

### Examples

```text
feat(backend): add matter creation endpoint
fix(frontend): correct date picker timezone handling
docs(readme): update installation steps
test(api): add contract tests for appointments endpoint
ci(workflow): add shell linting job
```

## Code Quality

```bash
make lint            # Run all linters
make lint/fix        # Auto-fix lint issues
make lint/md         # Lint markdown only
make lint/md/fix     # Fix markdown issues
make lint/shell      # Lint shell scripts
make format          # Format files with prettier
make format/check    # Check formatting without changes
```

### Linting Standards

| Target          | Tool              | Auto-fix     |
| --------------- | ----------------- | ------------ |
| Markdown        | markdownlint-cli2 | Yes          |
| Shell scripts   | shellcheck        | No (manual)  |
| JSON, YAML      | prettier          | Yes          |
| Commit messages | gitlint           | No (rewrite) |

## Pull Request Checklist

- [ ] All linters pass (`make lint`)
- [ ] Formatting is correct (`make format/check`)
- [ ] Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/)
- [ ] Documentation updated if this is a user-facing change

## Release Process

Releases are managed via `commit-and-tag-version`:

```bash
make release/dry-run  # Preview what would happen
make release/patch    # 0.1.0 -> 0.1.1
make release/minor    # 0.1.0 -> 0.2.0
make release/major    # 0.1.0 -> 1.0.0
```
