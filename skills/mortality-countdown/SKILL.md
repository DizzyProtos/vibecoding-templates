# Skill: Mortality Countdown

**Agent constraints:** Follow [safety-rules.md](./safety-rules.md) in this folder. Do not change production or break existing features.

## Purpose

Support the design of the **Mortality Countdown API**, which estimates the time until a user is 80 years old (based on age or birthdate) and exposes it as a countdown.

This is a darkly humorous project; keep implementation respectful, opt-in, and technically correct around date/time handling.

## When to Use This Skill

Use this skill when:

- Creating or modifying `MortalityProfile` or `CountdownSnapshot` models.
- Designing endpoints that accept age/birthdate and return countdown data.
- Implementing or refactoring the countdown/domain logic.
- Adding date/time tests, especially around boundary cases.

## Domain Guidelines

- **MortalityProfile**
  - Fields: `id`, `userId`, either `birthDate` or `ageYears`, `createdAt`, optional `timezone`.
  - Prefer storing a `birthDate` when available for more accurate calculations.
  - If `ageYears` is used, clearly document how it is interpreted (e.g. integer years, approximate).

- **CountdownSnapshot**
  - Not persisted; derived object with `yearsLeft`, `daysLeft`, `hoursLeft`, `secondsLeft` at a given `asOf` timestamp.
  - May also include `targetDate` (80th birthday) and a boolean `pastEighty`.

## API Design Preferences

- Core endpoints:
  - `POST /mortality-profiles` – create or update a mortality profile from current age or birthdate.
  - `GET /mortality-profiles/:userId` – return the stored profile.
  - `GET /mortality-profiles/:userId/countdown` – return a `CountdownSnapshot` relative to now (or an optional `asOf` query parameter).

- Response design:
  - For countdown responses, include:
    - Remaining time components (years, days, hours, seconds).
    - A clear `pastEighty` flag if the user is already beyond 80.
  - Avoid overly precise implied guarantees (e.g. treat this as a simple arithmetic estimate, not a prediction).

## Time & Date Handling

- Implement all countdown math in a pure domain service such as `mortalityCountdownService` under `src/domain/services`.
- Use a clock abstraction:
  - Inject a `Clock` or similar interface instead of calling `Date.now()` directly.
  - This makes tests deterministic and safe around time boundaries.
- Timezones:
  - If `timezone` is stored, use it consistently when computing the target 80th-birthday date.
  - Document how you handle users near midnight boundaries or date changes.

## Boundary Rules

- Clamp negative remaining time to zero (no negative years/days).
- When `pastEighty` is true:
  - All remaining-time components should be zero.
  - The API can still return the original profile and target date if helpful.

## Testing Guidance

- Suggest tests that:
  - Cover users just before and just after their 80th birthday.
  - Validate behavior when only `ageYears` is provided vs when `birthDate` is available.
  - Use fixed `asOf` timestamps via the clock abstraction for deterministic assertions.
  - Exercise timezone behavior when it is part of the profile.

## Tone & UX Considerations

- Be explicit in docs and messages that this is a playful, approximate countdown, not a real-life prediction.
- Avoid coercing or surprising users with this feature; treat it as opt-in.

