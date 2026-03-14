# Skill: Micro-Journal Prompt Curator

**Agent constraints:** Follow [safety-rules.md](./safety-rules.md) in this folder. Do not change production or break existing features.

## Purpose

Guide the design and curation of micro-journal prompts for the **Micro-Journal Prompt Generator API**, ensuring that prompts are:

- Short and easy to answer (3–5 minutes).
- Themed (e.g. gratitude, reflection, planning).
- Adapted to user preferences and context (weekday vs weekend, time-of-day).

## When to Use This Skill

Use this skill when:

- Creating or modifying the `Prompt`, `UserPreference`, or `PromptSession` entities.
- Designing or refining prompt selection logic and APIs.
- Generating seed prompts or test fixtures.

## Prompt Guidelines

- **Length**
  - One sentence or a short two-sentence prompt.

- **Tone**
  - Supportive, non-judgmental, reflective.
  - Avoid diagnostic, clinical, or harsh wording.

- **Tags**
  - Examples: `gratitude`, `reflection`, `planning`, `relationships`, `self-care`.

- **Example Prompts**
  - “What is one small thing you’re grateful for today?”
  - “What is one decision you made today that you’re proud of?”
  - “What’s a tiny step you could take tomorrow to support your future self?”

## Domain Model Guidelines

- `Prompt`
  - `id`, `text`, `tags: string[]`, optional `timeOfDayPreference` (`morning`, `evening`, `any`), optional `moodHint`, `source`.

- `UserPreference`
  - `userId`, `preferredTags: string[]`, optional `avoidTags: string[]`, optional `timeOfDayPreference`.

- `PromptSession`
  - `id`, `userId`, `promptId`, `generatedAt`, optional `context`.

## API Design Preferences

- Core endpoints:
  - `POST /prompts` – create a new prompt.
  - `GET /prompts/random` – random prompt, optionally filtered by `tag`.
  - `POST /users/:userId/preferences` – set or update preferences.
  - `GET /users/:userId/prompt` – return a prompt adapted to the user’s preferences and optional context (e.g., time-of-day).

- Response design:
  - For `GET /users/:userId/prompt`, include:
    - The selected prompt.
    - Metadata explaining selection (e.g. `matchedTags`, `timeOfDayMatch`).
  - Keep schemas stable and easy to version (e.g. `v1` namespace if the API grows).

## Testing Guidance

- Suggest tests where:
  - A user has strong preferences that are satisfied by available prompts.
  - No tags match preferences, and the system falls back to a general prompt.
  - Time-of-day preferences influence which prompts are chosen.

## Style

- Encourage self-compassion and small, actionable reflections.
- Avoid advice that is overly prescriptive or unrealistic; favor gentle, incremental change.

