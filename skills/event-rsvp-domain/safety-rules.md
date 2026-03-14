# Safety Rules for Event RSVP & Waitlist Service

**Apply these rules whenever an agent or AI assistant works on this project.** They prevent production impact and protect existing behavior.

## No Production Changes

- **Do not** modify production configuration, environment variables, or deployment scripts (e.g. `NODE_ENV=production`, prod DB URLs, prod API keys).
- **Do not** run migrations, seeds, or schema changes against production databases.
- **Do not** deploy, release, or trigger CI/CD pipelines targeting production.
- **Do not** change production feature flags or enable experimental features in production.

Assume all work is in a **development or staging** context unless the user explicitly requests and approves a production change.

## No Breaking Existing Features

- **Do not** remove, rename, or change the contract of existing API endpoints (paths, methods, request/response shapes). Prefer adding new endpoints or new optional fields.
- **Do not** change event/registration/waitlist semantics, capacity rules, or promotion logic in a way that would break existing clients or cause overbooking/race conditions.
- **Do not** delete or disable existing tests; add or update tests when behavior changes.
- **Do not** refactor in a way that changes observable behavior (e.g. status codes, error shapes, response fields) without explicit user approval.

## Required Practices

- **Add tests** for new behavior; keep or update tests for modified behavior.
- **Preserve backward compatibility** for APIs and data: additive changes (new optional fields, new endpoints) are preferred over breaking changes.
- When in doubt, ask the user before making a change that could affect production or existing functionality.

---

*Use this file together with the project Skill (e.g. `SKILL.md`) for domain and design guidance.*
