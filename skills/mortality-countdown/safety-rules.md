# Safety Rules for Mortality Countdown API

**Apply these rules whenever an agent or AI assistant works on this project.** They prevent production impact, protect existing behavior, and encourage sensitive handling of mortality-related features.

## No Production Changes

- **Do not** modify production configuration, environment variables, or deployment scripts (e.g. `NODE_ENV=production`, prod DB URLs, prod API keys).
- **Do not** run migrations, seeds, or schema changes against production databases.
- **Do not** deploy, release, or trigger CI/CD pipelines targeting production.
- **Do not** change production feature flags or enable experimental features in production.

Assume all work is in a **development or staging** context unless the user explicitly requests and approves a production change.

## No Breaking Existing Features

- **Do not** remove, rename, or change the contract of existing API endpoints (paths, methods, request/response shapes). Prefer adding new endpoints or new optional fields.
- **Do not** change semantics of `MortalityProfile` or `CountdownSnapshot` in a way that breaks existing clients or stored data.
- **Do not** delete or disable existing tests; add or update tests when behavior changes.
- **Do not** refactor in a way that changes observable behavior (status codes, error shapes, response fields) without explicit user approval.

## Sensitivity & Framing

- Treat mortality countdowns as an **opt-in, playful feature**, not a diagnostic or predictive tool.
- Avoid language that is alarmist, shaming, or prescriptive about life choices.
- Example-friendly phrasing: “Estimated time until age 80 based on simple math; not a prediction of lifespan.”

## Required Practices

- **Add tests** for new behavior; keep or update tests for modified behavior, especially around boundary and timezone cases.
- **Preserve backward compatibility** for APIs and data: additive changes (new optional fields, new endpoints) are preferred over breaking changes.
- When in doubt, ask the user before making a change that could affect production, existing functionality, or the tone of user-facing messaging.

---

*Use this file together with the project Skill (e.g. `SKILL.md`) for domain and design guidance.*

