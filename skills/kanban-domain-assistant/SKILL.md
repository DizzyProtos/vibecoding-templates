# Skill: Kanban Domain Assistant

## Purpose

Assist with the design and evolution of the **Minimal Kanban/Task Board backend**, focusing on:

- Simple, understandable workflow rules.
- Clear, predictable API behavior.
- Clean separation between domain logic and infrastructure details.

## When to Use This Skill

Use this skill when:

- Defining or modifying `Project`, `Column`, or `Task` entities.
- Changing task status transition rules or column ordering.
- Designing or refactoring endpoints for task creation and movement.

## Domain Guidelines

- **Columns**
  - Fields: `id`, `projectId`, `name`, `order`, optional `kind` (`todo`, `doing`, `done`).
  - `order` is a zero-based or one-based index used to sort columns within a project.

- **Tasks**
  - Fields: `id`, `projectId`, `columnId`, `title`, optional `description`, optional `assignee`, `position`.
  - `position` is used to order tasks within a column.

- **Status vs Column**
  - Prefer deriving logical status from the column kind, e.g. `status = column.kind`.
  - Avoid duplicating status logic in multiple places.

- **Transitions**
  - Default allowed path: `todo → doing → done`.
  - Invalid transitions (e.g. direct `todo → done`) should:
    - Be validated by a dedicated workflow service.
    - Return a structured `InvalidTransitionError` or similar in the domain layer.

## API Design Preferences

- Core endpoints:
  - `POST /projects`, `GET /projects/:id`.
  - `POST /projects/:projectId/columns`.
  - `POST /projects/:projectId/tasks`.
  - `POST /tasks/:taskId/move` with `{ toColumnId }`.

- Response design:
  - Use plural resource names.
  - Return normalized data (IDs and flat objects) instead of overly nested graphs for mutations.
  - Use meaningful HTTP status codes (`201` for create, `400` for invalid transitions, `404` for missing resources).

## Testing Guidance

- Suggest tests that:
  - Prevent skipping steps in the workflow unless explicitly configured.
  - Verify that `position` is updated correctly when moving tasks within the same column and across columns.
  - Cover edge cases such as moving the first or last task in a column.

## Style & Structure

- Prefer small, composable services (e.g. `taskWorkflowService`, `columnOrderService`) over monolithic modules.
- Encourage explicit type aliases or enums (e.g. `TaskStatus`, `ColumnKind`) instead of raw strings scattered through the code.

