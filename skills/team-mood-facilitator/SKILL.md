# Skill: Team Mood Facilitator

**Agent constraints:** Follow [safety-rules.md](./safety-rules.md) in this folder. Do not change production or break existing features.

## Purpose

Support the design and evolution of the **Team Mood & Check-In API** domain model, ensuring:

- Simple, psychologically safe check-in questions.
- Clear mood categories and scoring.
- Useful aggregation endpoints for teams.

## When to Use This Skill

Use this skill whenever you:

- Define or modify `CheckIn`, `Team`, `Member`, or `MoodSummary` entities.
- Design or refactor endpoints related to check-ins or summaries.
- Generate sample data or test scenarios for check-ins.

## Domain Guidelines

- **Check-in questions**
  - Short and answerable in under a minute.
  - Non-invasive; avoid clinical/medical language.
  - Focus on energy, stress, and general mood rather than diagnosis.

- **Mood scale**
  - Use an integer scale (e.g., 1–5 or 1–7).
  - Provide named labels for clarity, e.g.:
    - `1 = "Drained"`
    - `3 = "OK"`
    - `5 = "Energized"`

- **Entities**
  - `Team`: `id`, `name`, `timezone`.
  - `Member`: `id`, `name`, `email`, `teamId`, optional `role`.
  - `CheckIn`: `id`, `memberId`, `teamId`, `date`, `moodScore`, optional `comment`, optional `tags`.
  - `MoodSummary`: derived aggregation (average mood, count of check-ins, distribution).

## API Design Preferences

- Core endpoints:
  - `POST /teams`, `GET /teams/:id`.
  - `POST /teams/:teamId/check-ins` – create a new check-in.
  - `GET /teams/:teamId/check-ins?date=YYYY-MM-DD`.
  - `GET /teams/:teamId/summary?from=...&to=...`.

- Response design:
  - Prefer explicit field names and simple JSON structures.
  - Include `timezone` where relevant to explain date boundaries.
  - Avoid leaking ORM or MCP-specific details into API responses.

## Testing Guidance

- Always suggest:
  - Tests for minimum and maximum mood values.
  - Tests across multiple members and teams.
  - At least one test involving different timezones or date boundaries if applicable.

## Tone & Style

- Keep naming neutral and inclusive.
- Use observational wording in summaries (e.g. “Most of the team reported low energy today”) rather than judgmental phrasing.

