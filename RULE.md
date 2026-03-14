## Project: Vibecoding Workshop Templates

This repository contains example backends for a vibecoding workshop using Cursor. Each project is designed so that an MVP can be built in **2–3 hours** with strong AI assistance, while still producing clean, maintainable code.

The projects are:

1. **Team Mood & Check-In API**
2. **Minimal Kanban/Task Board Backend**
3. **Micro-Journal Prompt Generator API**
4. **AI-Powered Habit Tracker API**
5. **Content Curation & Summarization Service**
6. **Personal Finance Snapshot API**
7. **Event RSVP & Waitlist Service**

Each project has:

- A recommended **architecture and folder structure**.
- Expected **MCP integrations** (database + at least one external service).
- One or more **Cursor skills** capturing domain knowledge (in `skills/<project>/SKILL.md`).
- **Safety rules** (in `skills/<project>/safety-rules.md`) that the agent must follow: no production changes, no breaking existing features; apply these whenever working on that project.

---

## Shared Tech & Conventions

- **Language**: TypeScript with strict type checking enabled.
- **Runtime**: Node.js (LTS).
- **HTTP framework**: Express or Fastify (assistant may choose), but:
  - All HTTP concerns stay under `src/http`.
  - No business logic in route handlers.
- **Tests**: Jest or Vitest, with unit tests preferred for domain services.

### Common Folder Layout

For each project, prefer the following structure:

- `src/http`
  - `routes/` – route registration only (maps URL → controller).
  - `controllers/` – translate HTTP to use-case calls; no domain logic.
- `src/domain`
  - `entities/` – domain models.
  - `services/` – pure business logic and use-cases.
  - `repositories/` – repository interfaces.
- `src/infrastructure`
  - `db/` – concrete repository implementations (using MCP DB).
  - `messaging/`, `sync/`, or `external/` – adapters to external MCPs.
- `src/config` – configuration and environment variable parsing.
- `tests/` – test files mirroring the `src` structure.

### General Coding Rules

- Domain services must not:
  - Read environment variables directly.
  - Call MCP clients or log directly.
- Controllers must:
  - Accept validated DTOs.
  - Call domain services.
  - Map domain errors to HTTP responses.
- Repository interfaces live in `src/domain/repositories` and are implemented in `src/infrastructure/db`.

---

## Team Mood & Check-In API

Build an API for teams to submit daily check-ins with mood scores and short comments, and fetch aggregated summaries.

### Architecture & Rules

- Key entities:
  - `Team`: `id`, `name`, `timezone`.
  - `Member`: `id`, `name`, `email`, `teamId`, optional `role`.
  - `CheckIn`: `id`, `memberId`, `teamId`, `date`, `moodScore`, optional `comment`, optional `tags`.
  - `MoodSummary`: derived aggregation, not stored directly.
- Endpoints (MVP):
  - `POST /teams`, `GET /teams/:id`.
  - `POST /teams/:teamId/check-ins`.
  - `GET /teams/:teamId/check-ins?date=YYYY-MM-DD`.
  - `GET /teams/:teamId/summary?from=...&to=...`.
- All aggregation and streak logic lives in domain services under `src/domain/services`.

### Error Handling

- Map domain errors to HTTP status codes in controllers:
  - `ValidationError` → `400`.
  - `NotFoundError` → `404`.
  - Other errors → `500`, with logging in an infrastructure layer.

### Testing Expectations

- Unit tests for:
  - Mood aggregation summaries.
  - Streak / trend calculations.
  - Timezone-sensitive date handling where applicable.

---

## Minimal Kanban/Task Board Backend

Build a small backend for projects, columns, and tasks with basic workflow transitions (`todo → doing → done`).

### Architecture & Rules

- Key entities:
  - `Project`: `id`, `name`.
  - `Column`: `id`, `projectId`, `name`, `order`, optional `kind` (`todo`, `doing`, `done`).
  - `Task`: `id`, `projectId`, `columnId`, `title`, optional `description`, optional `assignee`, `position`.
- Workflow rules:
  - Allowed transitions: `todo → doing → done` by default.
  - Disallow skipping steps (e.g., `todo → done`) unless explicitly allowed by the domain service.
- Endpoints (MVP):
  - `POST /projects`, `GET /projects/:id`.
  - `POST /projects/:projectId/columns`.
  - `POST /projects/:projectId/tasks`.
  - `POST /tasks/:taskId/move` with `{ toColumnId }`.
- All transition logic and ordering live in a domain service (for example, `taskWorkflowService`) under `src/domain/services`.

### Testing Expectations

- Tests for:
  - Valid and invalid transitions.
  - Task ordering preservation when moving tasks within and across columns.

---

## Micro-Journal Prompt Generator API

Build an API that stores journaling prompts and returns context-aware prompts for users based on tags and preferences.

### Architecture & Rules

- Key entities:
  - `Prompt`: `id`, `text`, `tags: string[]`, optional `timeOfDayPreference` (`morning`, `evening`, `any`), optional `moodHint`, `source`.
  - `UserPreference`: `userId`, `preferredTags: string[]`, optional `avoidTags: string[]`, optional `timeOfDayPreference`.
  - `PromptSession`: `id`, `userId`, `promptId`, `generatedAt`, optional `context`.
- Endpoints (MVP):
  - `POST /prompts`.
  - `GET /prompts/random`.
  - `GET /prompts/random?tag=...`.
  - `POST /users/:userId/preferences`.
  - `GET /users/:userId/prompt`.
- Prompt selection and weighting logic lives in `src/domain/services/promptSelectionService`.

### Testing Expectations

- Tests for:
  - Tag filtering and preference handling.
  - Fallback behavior when no prompts match filters.
  - Deterministic behavior where randomization is controlled or seeded.

---

## AI-Powered Habit Tracker API

Build an API for habits with CRUD, streak calculation, and a daily summary. Optional: suggest best times via calendar/notification MCP.

### Architecture & Rules

- Key entities:
  - `Habit`: `id`, `name`, `userId`, optional `targetTime`, optional `frequency` (daily, weekly).
  - `HabitCompletion`: `id`, `habitId`, `date`, optional `notes`.
  - Streak and daily summary are derived in domain services.
- Endpoints (MVP):
  - `POST /habits`, `GET /habits`, `GET /habits/:id`, `PATCH /habits/:id`, `DELETE /habits/:id`.
  - `POST /habits/:habitId/completions` (record a completion for a date).
  - `GET /habits/:habitId/streak`.
  - `GET /users/:userId/daily-summary?date=YYYY-MM-DD`.
- All streak and summary logic lives in `src/domain/services`; persistence in `src/infrastructure/db`. Optional calendar/notification logic in `src/infrastructure/external`.

### Testing Expectations

- Unit tests for streak calculation (consecutive days, gaps, timezone).
- Tests for daily summary aggregation.

---

## Content Curation & Summarization Service

Build an API to submit URLs, fetch and store article metadata, and generate short summaries or tags.

### Architecture & Rules

- Key entities:
  - `Article`: `id`, `url`, `title`, `fetchedAt`, optional `summary`, optional `tags: string[]`, `rawContent?`.
  - Fetch and summarization are async; keep summarization logic pure in `src/domain/services`, external fetch in `src/infrastructure/external`.
- Endpoints (MVP):
  - `POST /articles` with `{ url }` – enqueue fetch and optionally summarization.
  - `GET /articles`, `GET /articles/:id`.
  - `GET /articles/:id/summary` (or summary inline in article response).
- Rules: pure services for summarization and tagging; log and handle errors for external HTTP/RSS in infrastructure only.

### Testing Expectations

- Unit tests for summary/tag generation logic with mocked content.
- Integration-style tests for fetch error handling and retries if applicable.

---

## Personal Finance Snapshot API

Build an API for transactions, basic categorization, and a high-level monthly snapshot (income, expenses, balance).

### Architecture & Rules

- Key entities:
  - `Transaction`: `id`, `userId`, `amount` (signed), `date`, `category`, optional `description`, optional `source`.
  - `Category`: `id`, `name`, `type` (income | expense).
  - Snapshot (totals, by category) is computed in a domain service.
- Endpoints (MVP):
  - `POST /transactions`, `GET /transactions?from=...&to=...`.
  - `GET /users/:userId/snapshot?month=YYYY-MM` (or `from`/`to`).
  - Optional: `GET /categories`, `POST /categories`.
- Domain rules: no negative balance semantics in core logic unless explicitly modeled; category validation in domain service. Use repositories for persistence.

### Testing Expectations

- Unit tests for snapshot calculations (sums, filtering by category and date range).
- Tests for category validation and invalid amount handling.

---

## Event RSVP & Waitlist Service

Build an API for events with capacity, attendee registration, and waitlist; optional email/messaging for confirmations and waitlist notifications.

### Architecture & Rules

- Key entities:
  - `Event`: `id`, `name`, `capacity`, `startAt`, optional `endAt`.
  - `Registration`: `id`, `eventId`, `attendeeId` or `email`, `status` (confirmed | waitlisted | cancelled).
  - All capacity and waitlist promotion logic lives in `eventService` (or `registrationService`); controllers only call services and map errors.
- Endpoints (MVP):
  - `POST /events`, `GET /events`, `GET /events/:id`.
  - `POST /events/:eventId/register` with `{ email }` – returns confirmed or waitlisted.
  - `POST /events/:eventId/cancel-registration` or `DELETE /registrations/:id`.
  - `POST /events/:eventId/promote-waitlist` (internal or admin: promote N from waitlist when capacity frees).
- Optional: `src/infrastructure/messaging` for email/Slack confirmations and waitlist notifications via MCP.

### Testing Expectations

- Unit tests for capacity logic (full event → waitlist), cancellation and promotion.
- Edge cases: overbooking prevention, concurrent registration handling if applicable.

---

## Cursor-Specific Guidance

- When adding new endpoints:
  1. Create or update route in `src/http/routes`.
  2. Implement controller in `src/http/controllers`.
  3. Delegate to a domain service in `src/domain/services`.
- When integrating MCP:
  - Place DB-related code under `src/infrastructure/db`.
  - Place external integrations (email, Slack, HTTP APIs, calendar, quotes, filesystem) under `src/infrastructure/messaging`, `src/infrastructure/sync`, or `src/infrastructure/external`.
- Use the provided skills in `skills/` to guide domain modeling, naming, and test scenario design. For each project, also apply the corresponding `skills/<project>/safety-rules.md`: no production changes, no breaking existing features.

