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

## Instructions for MAUI skills

Use the skills in `Maui\Skills\...` before answering questions or changing code in this area.

### Skill organization

1. Keep the existing MAUI-specific skills in their current top-level `Maui\Skills\maui-*` folders. These already represent the MAUI category and should not be moved just to fit another folder layout.
2. Put new Git-oriented skills under `Maui\Skills\git\...`.
3. Put new reusable non-MAUI skills under `Maui\Skills\generic\...`.
4. Put acceptance-criteria and other planning-oriented skills under `Maui\Skills\planning\...`.
5. When a skill includes bundled PowerShell helpers, keep them in a sibling `scrips` folder to match the existing changelog skill packaging pattern.

### Category routing

| Category | Path pattern | Use when |
| --- | --- | --- |
| MAUI | `Maui\Skills\maui-*` | The request is directly about .NET MAUI APIs, controls, app behavior, performance, storage, notifications, permissions, theming, or testing. |
| Git | `Maui\Skills\git\...` | The request is about commits, branches, tags, release notes, changelog generation, or versioning workflows. |
| Generic | `Maui\Skills\generic\...` | The request is reusable across projects and is not primarily MAUI-specific, Git-specific, or planning-oriented. |
| Planning | `Maui\Skills\planning\...` | The request is about acceptance criteria, scenario discovery, or planning work that should happen before implementation. |

### Routing rules

1. Start with `maui-current-apis` before generating or editing MAUI code so responses stay aligned with the project's target framework and current APIs.
2. Add the feature skill that matches the task. Use multiple skills when a request spans more than one area.
3. Route Git workflows to the `git` category, reusable cross-project workflows to `generic`, and acceptance-criteria work to `planning`.
4. Prefer the MAUI skills over generic guidance when the task is clearly MAUI-specific.
5. Do not rewrite or reorganize the existing `maui-*` skill folders unless the request is explicitly about those skills themselves.

### MAUI skill guide

| Skill | Short description | Use when | Why |
| --- | --- | --- | --- |
| `maui-current-apis` | Guardrail for current .NET MAUI APIs and .NET 10 changes. | Reviewing existing MAUI code, generating new MAUI code, or checking whether an API is still current. | It helps avoid deprecated controls, outdated methods, and version mismatches before they land in code. |
| `maui-app-lifecycle` | Window lifecycle, foreground/background transitions, and state restoration. | Handling `Created`, `Activated`, `Stopped`, `Resumed`, `Destroying`, or platform lifecycle hooks. | Lifecycle mistakes often cause duplicate initialization, lost state, or expensive work at the wrong time. |
| `maui-collectionview` | Data-driven lists and grids with templates, selection, refresh, and incremental loading. | Building or debugging scrollable lists, grouped lists, swipe actions, or grid-style item displays. | `CollectionView` is the modern MAUI list control and has important virtualization and template rules. |
| `maui-dependency-injection` | Service registration, lifetimes, constructor injection, and MAUI-specific DI pitfalls. | Wiring services in `MauiProgram.cs`, registering pages/viewmodels, or troubleshooting resolution problems. | Correct lifetimes and registration patterns keep navigation, testability, and platform services predictable. |
| `maui-local-notifications` | Client-side notifications scheduled or shown on-device. | Adding reminder notifications, notification channels, or foreground notification handling. | Local notifications need platform-specific setup and permission handling that is easy to miss. |
| `maui-localization` | RESX-based localization, platform declarations, RTL layout, and language resource setup. | Adding multi-language support, resource files, localized app metadata, or right-to-left behavior. | Localization in MAUI spans resources, project settings, and platform manifests, not just translated strings. |
| `maui-performance` | Compiled bindings, layout efficiency, profiling, trimming, and Native AOT readiness. | Investigating slow UI, slow startup, excessive memory use, or publish-size issues. | Performance work in MAUI depends on measurable guidance and framework-specific deployment constraints. |
| `maui-permissions` | Runtime permission checks, requests, statuses, and platform prompts. | Requesting camera, microphone, storage, or similar platform permissions. | Permission UX and platform differences affect reliability and user trust. |
| `maui-push-notifications` | Remote notifications with FCM, APNS, backend registration, and Azure Notification Hubs. | Building end-to-end push delivery from server to device. | Push requires coordinated client, platform, and backend setup, and failures are often silent. |
| `maui-secure-storage` | Small encrypted secrets with `SecureStorage.Default` and testable wrappers. | Storing tokens, credentials, or other sensitive key/value data. | Secure storage has platform-specific backup, keychain, and exception behavior that should be handled explicitly. |
| `maui-speech-to-text` | Voice input with MAUI/CommunityToolkit speech recognition patterns. | Adding dictation, microphone-driven text entry, or speech recognition flows. | Speech features depend on permissions, cancellation, and event handling that can leak or fail subtly. |
| `maui-sqlite-database` | Local relational storage with `sqlite-net-pcl`, DI, and connection lifetime guidance. | Adding offline data storage, CRUD services, or local database persistence. | SQLite setup in MAUI has path, package, and connection-lifetime choices that directly affect correctness. |
| `maui-theming` | Light/dark themes, resource dictionaries, and runtime theme switching. | Implementing dark mode, app theme resources, or branded theme variants. | Theme work depends on the right resource pattern or changes won't update consistently at runtime. |
| `maui-unit-testing` | Unit testing patterns for viewmodels and MAUI-facing services. | Creating or fixing tests for viewmodels, service abstractions, or MAUI app logic. | Good test structure avoids static UI dependencies and keeps MAUI logic testable off-device. |

### Coordination notes

- Use `maui-current-apis` together with feature skills when a request involves code generation or migration.
- Pair `maui-permissions` with `maui-speech-to-text`, `maui-local-notifications`, or `maui-push-notifications` when the task includes permission prompts.
- Pair `maui-dependency-injection` with most feature skills when the request involves service registration or testability.
- Use planning skills first when the user wants to define behavior or acceptance criteria before code exists.
- Use Git skills alongside MAUI skills when the request includes release notes, commit hygiene, or versioning work tied to MAUI changes.
