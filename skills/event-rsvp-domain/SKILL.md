# Skill: Event RSVP & Waitlist Domain

## Purpose

Support the design and evolution of the **Event RSVP & Waitlist Service**, ensuring:

- Clear event and registration models with capacity and waitlist semantics.
- All capacity and promotion logic in one place (e.g., `eventService` or `registrationService`).
- Well-defined edge cases: overbooking prevention, cancellation, waitlist promotion.

## When to Use This Skill

Use this skill when:

- Defining or modifying `Event`, `Registration`, or waitlist/promotion logic.
- Designing endpoints for events, registration, cancellation, and waitlist promotion.
- Generating test scenarios for capacity, overbooking, and concurrent registration.

## Domain Guidelines

- **Event**
  - Fields: `id`, `name`, `capacity` (integer), `startAt`, optional `endAt`.
  - Capacity is fixed for MVP; no overbooking.

- **Registration**
  - Fields: `id`, `eventId`, `attendeeId` or `email`, `status` (`confirmed` | `waitlisted` | `cancelled`), optional `createdAt`.
  - One active registration per (event, email) — re-register = idempotent or 400.

- **Rules**
  - If `confirmedCount < capacity`, new registration → `confirmed`.
  - If `confirmedCount >= capacity`, new registration → `waitlisted`.
  - Cancellation: set registration to `cancelled`; optionally promote one from waitlist (FIFO) in the same transaction or a follow-up call.
  - Promote-waitlist: move up to N waitlisted registrations to `confirmed` when capacity allows.

## API Design Preferences

- Endpoints:
  - `POST /events`, `GET /events`, `GET /events/:id` (include `confirmedCount`, `waitlistCount` if desired).
  - `POST /events/:eventId/register` with `{ email }` — return 201 + `{ status: "confirmed" }` or `{ status: "waitlisted" }`.
  - `POST /events/:eventId/cancel-registration` with `{ email }` or `DELETE /registrations/:id`.
  - `POST /events/:eventId/promote-waitlist` with `{ count?: number }` (admin or internal).
- Use 409 Conflict when event is full and no waitlist, or when already registered; 404 for unknown event.

## Testing Guidance

- Tests for: register until full → next goes to waitlist; cancel one → one promoted from waitlist; promote-waitlist respects capacity and FIFO.
- Edge cases: double registration (idempotent or 400); cancel already-cancelled; promote more than available capacity (cap at available).

## Style

- Use explicit status values; avoid overloading "pending". Optional: messaging (email/Slack) for confirmations and waitlist notifications in `src/infrastructure/messaging` via MCP.
