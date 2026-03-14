# Skill: Waifu Image Generator

**Agent constraints:** Follow [safety-rules.md](./safety-rules.md) in this folder. Do not change production or break existing features.

## Purpose

Support the design and evolution of the **Waifu Image Generator API**, a playful backend where users define their "ideal waifu" via traits and the system generates image prompts and metadata using OpenAI (or another image-generation MCP).

The goal is fun, but the architecture should stay clean, testable, and consistent with the workshop conventions.

## When to Use This Skill

Use this skill when:

- Defining or modifying `WaifuProfile`, `WaifuImageRequest`, or `WaifuImage` entities.
- Designing or refactoring endpoints related to waifu profiles or image generation.
- Working on prompt-building logic, style presets, or content-safety behavior.
- Integrating or mocking OpenAI/image-generation MCPs in this project.

## Domain Guidelines

- **WaifuProfile**
  - Fields: `id`, `userId`, `name`, `traits: string[]`, optional `backstory`, `createdAt`.
  - Traits should be simple, descriptive strings (e.g. `"tsundere"`, `"cyberpunk hacker"`, `"cozy librarian"`).
  - Backstory is optional flavor text, not required for prompt-building.

- **WaifuImageRequest**
  - Fields: `id`, `profileId`, `prompt`, `stylePreset`, `status` (`pending`, `completed`, `failed`), `requestedAt`.
  - `prompt` is the fully composed text sent to the image provider (after trait fusion and safety filters).
  - `stylePreset` can be a small enum-like set (e.g. `anime`, `watercolor`, `pixel-art`).

- **WaifuImage**
  - Fields: `id`, `requestId`, `imageUrl` or `imageId`, `provider`, `createdAt`, optional `nsfwFlag`.
  - Store only metadata and references; do not embed large binary data directly in responses.

## API Design Preferences

- Core endpoints:
  - `POST /waifus` – create or update a waifu profile with traits and optional backstory.
  - `GET /waifus/:id` – fetch a profile, plus its latest image metadata if available.
  - `POST /waifus/:id/generate-image` – enqueue an image generation request for a profile.
  - `GET /waifu-images/:id` – fetch generated image metadata by image ID.

- Response design:
  - Keep responses small and focused: profile data + references to images, not full image payloads.
  - Surface image generation status (`pending`, `completed`, `failed`) clearly in responses.
  - Avoid leaking provider-specific fields; normalize to a simple shape (`imageUrl`, `provider`, timestamps, flags).

## Architecture & Layering

- All prompt-building, style selection, and content-safety decisions live in a domain service
  such as `waifuImageService` under `src/domain/services`.
- Controllers:
  - Validate request DTOs.
  - Call domain services.
  - Map domain errors to HTTP status codes.
- Infrastructure:
  - Actual OpenAI/MCP image calls live under `src/infrastructure/external` (for example, `openAiImageClient`).
  - Use interfaces so providers can be swapped or mocked in tests.

## Prompt-Building Guidelines

- Combine:
  - Character traits.
  - Optional backstory fragments.
  - Style preset and optional mood descriptors.
- Keep prompts:
  - Descriptive but concise.
  - Free of disallowed or unsafe content (apply filters before sending to providers).
- Allow room for deterministic behavior in tests (e.g. injectable random/seed or pure functions that assemble prompts).

## Testing Guidance

- Suggest tests that:
  - Cover prompt-building behavior for different trait sets and style presets.
  - Assert that NSFW/safety flags are set when traits/backstories require it.
  - Verify that controllers never talk directly to the OpenAI client; they must go through domain services.
  - Ensure failure cases (provider errors, invalid traits) are surfaced as clear domain errors, not generic 500s.

## Tone & Safety

- Keep naming lighthearted and clearly fictional.
- Avoid realistic personal data in examples; use obviously fake names and scenarios.
- Ensure content-safety checks are always mentioned when integrating image providers.

