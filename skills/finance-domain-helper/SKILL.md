# Skill: Finance Domain Helper

## Purpose

Support the design and evolution of the **Personal Finance Snapshot API**, ensuring:

- Consistent transaction and category models.
- Correct snapshot semantics (income vs expense, date ranges, categories).
- Sensible validation and domain rules (e.g., category types, amount handling).

## When to Use This Skill

Use this skill when:

- Defining or modifying `Transaction`, `Category`, or snapshot/aggregation logic.
- Designing endpoints for transactions, categories, and monthly (or range) snapshots.
- Generating sample data or test scenarios for finance flows.

## Domain Guidelines

- **Transaction**
  - Fields: `id`, `userId`, `amount` (signed: positive = income, negative = expense, or separate `type` + absolute value), `date`, `category`, optional `description`, optional `source`.
  - Use a single currency per user or document multi-currency if needed (MVP: single currency).

- **Category**
  - Fields: `id`, `name`, `type` (`income` | `expense`).
  - Examples: income — "Salary", "Freelance"; expense — "Food", "Transport", "Rent".

- **Snapshot**
  - For a date range: total income, total expenses, balance (income − expenses); optional breakdown by category.
  - All calculations in a domain service; no raw SQL aggregation in controllers.

- **Validation**
  - Category must exist and match transaction type (income vs expense).
  - Amount non-zero; date within reasonable range (e.g., not future for past-only books).

## API Design Preferences

- Endpoints:
  - `POST /transactions`, `GET /transactions?from=...&to=...` (and optional `category`, `userId`).
  - `GET /users/:userId/snapshot?month=YYYY-MM` or `?from=...&to=...`.
  - Optional: `GET /categories`, `POST /categories`.
- Use decimal or integer cents for amounts to avoid float issues; document unit (e.g., cents or main unit).

## Testing Guidance

- Unit tests for snapshot: only income, only expenses, mixed; filter by category and date range.
- Tests for category validation: invalid category, wrong type (income category for negative amount).
- Edge cases: empty range, single transaction, zero amounts if allowed.

## Style

- Neutral, precise naming (e.g., "expense" not "spend"). Avoid financial advice; stick to recording and aggregating data.
