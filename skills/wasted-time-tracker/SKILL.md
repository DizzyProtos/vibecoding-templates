# Skill: Wasted Time Tracker

**Agent constraints:** Follow [safety-rules.md](./safety-rules.md) in this folder. Do not change production or break existing features.

## Purpose

Guide the design of the **Wasted Time Tracker API**, which lets users start a "wasted time" session, track elapsed minutes, and get playful suggestions for how that time could have been used instead.

The vibe is humorous, but the implementation should keep clean separation between HTTP, domain logic, and persistence.

## When to Use This Skill

Use this skill when:

- Creating or modifying `WasteSession` or `AlternativeUse` entities.
- Implementing or refactoring endpoints that start, stop, or inspect waste sessions.
- Designing the suggestion logic that maps durations to alternative uses.
- Adding tests around time calculations and session lifecycle constraints.

## Domain Guidelines

- **WasteSession**
  - Fields: `id`, `userId`, `startedAt`, optional `endedAt`, derived `durationMinutes`, optional `activityLabel`.
  - `durationMinutes` should be computed in domain services, not stored directly as the source of truth.
  - `activityLabel` is optional text describing what the user was actually doing (for laughs or analytics).

- **AlternativeUse**
  - Fields: `id`, `description`, `minutesRequiredMin`, `minutesRequiredMax`, optional `category`.
  - Examples of `description`:
    - `"Cooked a simple pasta dinner."`
    - `"Taken a short walk outside."`
    - `"Read a couple of book chapters."`

## API Design Preferences

- Core endpoints:
  - `POST /waste-sessions/start` – start a new session for a user.
  - `POST /waste-sessions/:id/stop` – stop a session and record `endedAt`.
  - `GET /waste-sessions/:id` – fetch session data with computed `durationMinutes`.
  - `GET /waste-sessions/:id/alternatives` – return fun alternative uses for that duration.

- Response design:
  - For session responses, include:
    - `startedAt`, `endedAt` (if present), `durationMinutes`.
  - For alternatives responses, return a list of `AlternativeUse` items filtered or weighted by duration.
  - Prefer small, flat JSON payloads over deeply nested structures.

## Architecture & Time Handling

- Keep time math in a dedicated domain service (for example, `wasteSessionService`) under `src/domain/services`.
- Use a clock abstraction:
  - Inject a `Clock` or similar dependency into services so tests can control "now".
  - Avoid calling `Date.now()` or `new Date()` directly in core business logic.
- Repositories:
  - `WasteSession` and `AlternativeUse` persistence should live behind repository interfaces in `src/domain/repositories` with implementations in `src/infrastructure/db`.

## Session Lifecycle Rules

- A session should have exactly one `startedAt`.
- Stopping a session:
  - Is idempotent or gracefully handled (no double-stop crashes).
  - Should not produce negative or obviously incorrect durations.
- You may optionally enforce a rule that a user can have at most one active session at a time; if so, return a domain error when trying to start another.

## Testing Guidance

- Suggest tests that:
  - Verify duration calculation for short (a few minutes) and long (hours) sessions.
  - Check behavior when `endedAt` is missing (active session) versus present (completed session).
  - Validate that alternative suggestions are chosen based on `durationMinutes` ranges.
  - Cover invalid flows (double stop, overlapping sessions if restricted).

## Tone & Style

- Keep suggestion text light, non-judgmental, and clearly humorous.
- Avoid shaming language; focus on playful “you could have…” framing.

