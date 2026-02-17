# Roadmap

Development roadmap for Practice Desk.

Want to help? Pick something from **Planned Features** below and open a PR. See
[CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## Current Version: v0.1.0

Project infrastructure and analysis phase. No application code yet.

Key capabilities:

- **CI/CD automation** — GitHub Actions with auto-issue management
- **Linting pipeline** — Markdown, shell scripts, formatting
- **Release automation** — Semantic versioning with auto-generated changelog
- **Conventional Commits** — Enforced commit message format

See [CHANGELOG.md](CHANGELOG.md) for full version history.

## Planned Features

### v0.2.0 — Analysis & Design

- [ ] **Persona definitions** — Legal vertical user types (managing partner, associate, paralegal,
      client)
- [ ] **User stories** — Core workflows (matter management, document collection, agenda, client
      communication)
- [ ] **Feature inventory** — Prioritized features (UMFP / post-UMFP / out of scope)
- [ ] **Screen inventory** — Screen definitions with wireframes and data requirements
- [ ] **Data model** — Entity model and contract schemas (YAML as SSOT)
- [ ] **API design** — REST + WebSocket specifications

### v0.3.0 — Backend Foundation

- [ ] **FastAPI backend** — Python with clean architecture (hexagonal)
- [ ] **SQLite persistence** — Repository adapters
- [ ] **Domain models** — SQLModel entities from contract schemas
- [ ] **Test pyramid** — Unit, integration, contract, smoke, E2E tests

### v0.4.0 — Frontend Foundation

- [ ] **React frontend** — TypeScript with clean architecture
- [ ] **Design system** — Component library with semantic tokens
- [ ] **State management** — Zustand stores with reactive subscriptions
- [ ] **WebSocket client** — Real-time updates

### v0.5.0 — Core Workflow

- [ ] **Matter management** — Create, track, close legal cases
- [ ] **Document collection** — Request, receive, review documents
- [ ] **Appointment scheduling** — Calendar with deadlines
- [ ] **Client portal** — Client-facing access for document upload and status

## Future Ideas

- [ ] **Multi-tenant** — Support for multiple practice types (legal, fiscal, labor)
- [ ] **WhatsApp integration** — Client communication channel
- [ ] **Email notifications** — Automated reminders and updates
- [ ] **Reporting dashboard** — KPIs and analytics

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on submitting features and bug fixes.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.
