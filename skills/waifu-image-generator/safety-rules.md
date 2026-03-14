# Safety Rules for Waifu Image Generator API

**Apply these rules whenever an agent or AI assistant works on this project.** They prevent production impact, protect existing behavior, and encourage safe use of image-generation providers.

## No Production Changes

- **Do not** modify production configuration, environment variables, or deployment scripts (e.g. `NODE_ENV=production`, prod DB URLs, prod API keys).
- **Do not** run migrations, seeds, or schema changes against production databases.
- **Do not** deploy, release, or trigger CI/CD pipelines targeting production.
- **Do not** change production feature flags or enable experimental features in production.

Assume all work is in a **development or staging** context unless the user explicitly requests and approves a production change.

## No Breaking Existing Features

- **Do not** remove, rename, or change the contract of existing API endpoints (paths, methods, request/response shapes). Prefer adding new endpoints or new optional fields.
- **Do not** change entity semantics (e.g. `WaifuProfile`, `WaifuImageRequest`, `WaifuImage` fields) in a way that breaks existing clients or stored data.
- **Do not** delete or disable existing tests; add or update tests when behavior changes.
- **Do not** refactor in a way that changes observable behavior (status codes, error shapes, response fields) without explicit user approval.

## Content & Provider Safety

- **Do not** bypass content-safety checks or filters when integrating OpenAI or other image providers.
- **Do not** hard-code provider API keys in the codebase; read them via configuration and keep them out of source control.
- When generating or testing prompts:
  - Keep them fictional and non-targeted at real individuals.
  - Avoid disallowed or harmful content categories as defined by the provider.
- Prefer abstractions and configuration that make it easy to adjust safety policies without invasive code changes.

## Required Practices

- **Add tests** for new behavior; keep or update tests for modified behavior, especially around prompt-building and safety flags.
- **Preserve backward compatibility** for APIs and data: additive changes (new optional fields, new endpoints) are preferred over breaking changes.
- When in doubt, ask the user before making a change that could affect production, existing functionality, or safety posture.

---

*Use this file together with the project Skill (e.g. `SKILL.md`) for domain and design guidance.*

