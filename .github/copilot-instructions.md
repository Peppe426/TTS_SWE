# Copilot Instructions

## Repository identity

- This repository is the foundation for a **Swedish community speech collection platform** that may later support Swedish TTS work.
- It is **not** an active model-training, inference, or production API codebase today.
- Treat documentation, governance, scope, and contribution guidance as first-class project artifacts.

## Current repository state

- The repository is still in the **planning and foundation** stage.
- It is documentation-first right now: there is no application, backend, model-training, build, test, or lint implementation checked in yet.
- Before proposing implementation details, read the core docs and anchor suggestions to the current milestone instead of inventing later-phase architecture.

## Source-of-truth documents

- `README.md` is the entry point and states the current engineering priority.
- `docs/PROJECT_SCOPE.md` defines the first milestone and explicit non-goals.
- `docs/ROADMAP.md` defines the numbered phases and the current milestone.
- `docs/DATA_GOVERNANCE.md` constrains consent, privacy, moderation, storage, licensing, and future reuse.
- `CONTRIBUTING.md` defines the current contribution model and makes clear that documentation, research, governance, and planning work are welcome now.

## Current milestone

- The active milestone spans **Phase 1 - Mobile recording app MVP** and **Phase 2 - Backend and data pipeline**.
- Current work should center on:
  1. mobile recording flows for Swedish contributors
  2. prompt presentation and short guided answers
  3. consent capture and recording guidance
  4. upload, metadata, and storage planning
  5. moderation, review, and quality checks
  6. dataset curation and export preparation

## What to avoid assuming

- Do not assume a fixed TTS architecture, production inference API, or large-scale training stack.
- Do not present model-training code or model-selection decisions as current scope.
- Do not assume unrestricted public voice uploads are already approved or live.
- Do not mix in external datasets until licensing and redistribution rights have been reviewed.

## Data and governance requirements

- Keep the project **Swedish-first**. Prompts, flows, data choices, and product decisions should prioritize Swedish speech collection needs.
- Consent is mandatory. Any proposed collection or reuse flow must clearly explain:
  1. what is recorded
  2. what metadata is collected
  3. how recordings are stored
  4. whether recordings may be published
  5. whether recordings may be used for training or evaluation
  6. whether removal can be requested
- Minimize personal data collection and separate contributor identity from dataset assets wherever possible.
- Treat moderation and quality as core workflow requirements, including the ability to reject or quarantine problematic recordings.
- For external datasets, check redistribution rights, commercial-use rights, model-training rights, attribution requirements, and derivative-work rules before suggesting integration.

## Contribution guidance

- Favor contributions that improve documentation, scope clarity, governance, Swedish prompt design, product planning, backend planning, and open-data research.
- If implementation work is requested, frame it as early scaffolding for the Phase 1 and Phase 2 system direction rather than as a later-phase TTS product.

## Build, test, and lint commands

- No build, test, or lint commands are configured in this repository yet.
- There is no executable test suite in the repo at the moment.

## Documentation consistency

- When updating repo docs, keep `README.md`, `docs/PROJECT_SCOPE.md`, `docs/ROADMAP.md`, `docs/DATA_GOVERNANCE.md`, and `CONTRIBUTING.md` consistent with each other.

## Testing conventions
- Use NUnit + FluentAssertions [7.0.0].
- Keep the FluentAssertions package reference in `src\tests\Tests.Core\Tests.Core.csproj`. Additional test projects should reference `Tests.Core` instead of adding their own FluentAssertions package directly.
- Structure tests with `Given / When / Then` comments.
- Name tests as `Should_<ExpectedBehavior>_When_<Condition>`.
- For exceptions, defer execution (`Action`, `Func<T>`, `Func<Task>`) and assert via FluentAssertions.
- Reference details in `docs/Test structure.md`.

## Code style

- File-scoped namespaces (`namespace Foo;`)
- Primary constructors for DI (e.g., `sealed class Foo(IDep dep) : ControllerBase`)
- `sealed` on concrete classes by default; `sealed record` for domain events and DTOs
- `ArgumentNullException.ThrowIfNull(...)` / `ArgumentException.ThrowIfNullOrWhiteSpace(...)` guard clauses
- Prefer domain-specific exceptions over throwing framework argument exceptions directly from domain code. When validation fails because of an underlying exception such as `ArgumentNullException`, wrap it as the inner exception.
- Place project-specific domain exceptions in an `Exceptions` directory inside the owning project.

### Commit messages

- Use **Conventional Commits** when suggesting or creating commit messages.
- Preferred commit types in this repository are: `feat`, `chore`, `docs`, `test`, and `issue`.
- **Do not suggest `fix` by default.** `fix` is reserved for actual bug fixes only.
- Keep subjects short, imperative, and lowercase where practical (for example: `feat: add redis stream health check`).
- If a scope helps clarity, use the standard Conventional Commits shape: `<type>(<scope>): <subject>`.
- If you are unsure how to phrase a commit, follow the Conventional Commits specification and choose the closest allowed type from the list above.