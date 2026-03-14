# Skill: Content Curation & Summarization

**Agent constraints:** Follow [safety-rules.md](./safety-rules.md) in this folder. Do not change production or break existing features.

## Purpose

Support the design and evolution of the **Content Curation & Summarization Service**, ensuring:

- Clear separation between fetching (infrastructure) and summarizing/tagging (domain or dedicated service).
- Consistent article and summary models.
- Style and schema choices that suit the target audience (e.g., "for busy PMs": short, actionable summaries).

## When to Use This Skill

Use this skill when:

- Defining or modifying `Article`, summary, or tag models.
- Designing endpoints for submitting URLs, fetching content, and retrieving summaries.
- Implementing or refactoring summarization and tagging logic (prompts, response schemas).

## Domain Guidelines

- **Article**
  - Fields: `id`, `url`, `title`, `fetchedAt`, optional `summary`, optional `tags: string[]`, optional `rawContent` (or store externally).
  - URL should be normalized and deduplicated where possible.

- **Summarization**
  - Keep summarization logic pure where possible: input = text (or URL resolved to text), output = summary + optional tags.
  - Prefer a stable schema for summary (e.g., `{ summary: string, tags: string[] }`) to simplify clients and tests.

- **Fetch**
  - Fetching and parsing (HTML, RSS) belong in `src/infrastructure/external`; handle timeouts, redirects, and parse errors there.
  - Domain layer consumes already-fetched content (e.g., title + body text).

## API Design Preferences

- Endpoints:
  - `POST /articles` with `{ url }` – trigger fetch and optionally summarization (return 202 + job id or synchronous for MVP).
  - `GET /articles`, `GET /articles/:id` (include summary and tags when ready).
  - Optional: `GET /articles/:id/summary` for polling until summary is ready.
- Prefer idempotent behavior for same URL (e.g., return existing article or update).

## Testing Guidance

- Unit tests for summarization/tagging with fixed input text; mock external fetch.
  - Test short vs long content; test failure/empty content.
- Integration or adapter tests for fetch layer: invalid URL, timeout, non-HTML response.

## Style

- Summaries: concise (e.g., 2–4 sentences for "busy PMs"); neutral tone; avoid marketing language. Tags: lowercase, few (3–7 per article).
