# Skill: Habit Domain Coach

## Purpose

Support the design and evolution of the **AI-Powered Habit Tracker API**, ensuring:

- Clear, consistent habit and completion entities.
- Correct streak and daily-summary semantics.
- Validation rules and examples that avoid common pitfalls (e.g., timezone, duplicate completions).

## When to Use This Skill

Use this skill when:

- Defining or modifying `Habit`, `HabitCompletion`, or derived concepts (streak, daily summary).
- Designing or refactoring endpoints for habits, completions, streaks, or summaries.
- Generating sample data or test scenarios for habits and completions.

## Domain Guidelines

- **Habits**
  - Fields: `id`, `name`, `userId`, optional `targetTime` (e.g., "09:00"), optional `frequency` (`daily`, `weekly`).
  - Name should be short and action-oriented (e.g., "Morning run", "Read 10 min").

- **Completions**
  - One completion per habit per calendar day (or per period for weekly habits).
  - Fields: `id`, `habitId`, `date` (YYYY-MM-DD), optional `notes`.
  - Validate: no duplicate `(habitId, date)`; date not in the future.

- **Streak**
  - Consecutive days with at least one completion for the habit.
  - Define clearly: break on missing day; timezone is that of the user or server (document in API).

- **Daily summary**
  - For a given user and date: list habits, completion status per habit, and optionally current streak per habit.

## API Design Preferences

- Endpoints:
  - `POST /habits`, `GET /habits`, `GET /habits/:id`, `PATCH /habits/:id`, `DELETE /habits/:id`.
  - `POST /habits/:habitId/completions` with `{ date?, notes? }` (default date = today).
  - `GET /habits/:habitId/streak`.
  - `GET /users/:userId/daily-summary?date=YYYY-MM-DD`.
- Use consistent date format (ISO 8601 date only) and document timezone behavior.

## Testing Guidance

- Suggest tests for:
  - Streak: consecutive days, one missed day breaks streak, no completions = 0 streak.
  - Daily summary: mix of completed and not completed habits; empty day.
  - Validation: duplicate completion for same habit+date returns 400 or idempotent update.

## Style

- Prefer positive framing (e.g., "completed" vs "failed"). Optional "best time" suggestions belong in infrastructure (e.g., calendar MCP), not in core domain types.
