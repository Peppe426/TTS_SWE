# Swedish TTS

A project to build the foundations for better **Swedish text-to-speech (TTS)**

## Status

This project is at the **planning and foundation** stage.

The current priority is:

**Build a mobile app plus backend/data pipeline for collecting Swedish speech recordings from volunteers.**

## Documentation

This repository currently uses these core documents:

- [`README.md`](README.md) - project overview and starting point
- [`CONTRIBUTING.md`](CONTRIBUTING.md) - how people can help
- [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md) - community behavior expectations
- [`docs\PROJECT_SCOPE.md`](docs/PROJECT_SCOPE.md) - what phase 1 includes and excludes
- [`docs\ROADMAP.md`](docs/ROADMAP.md) - project phases and milestones
- [`docs\DATA_GOVERNANCE.md`](docs/DATA_GOVERNANCE.md) - consent, privacy, licensing, and data handling rules

## Why this project exists

Swedish is not as well served as larger languages in open speech and voice ecosystems. The goal of this project is to create a strong, community-driven foundation that can eventually support:

- Swedish speech datasets
- Swedish pronunciation and transcript resources
- Future Swedish TTS experiments
- Open collaboration around speech technology

## What this project is

- A **community data collection project**
- A future foundation for **Swedish TTS research and prototyping**
- A place to organize contributors, recordings, data rules, and roadmap decisions

## Roadmap

### Phase 1 - Mobile recording app MVP

Build a mobile experience where contributors can record Swedish speech.

Goals:

- Let users read prompted Swedish phrases
- Let users answer curated Swedish questions
- Show consent and recording guidance clearly
- Upload recordings and basic metadata

### Phase 2 - Backend and data pipeline

Turn raw uploads into usable dataset assets.

Goals:

- Accept uploads and store metadata
- Track consent records
- Run basic quality checks
- Export approved recordings into a training-friendly format

### Phase 3 - Open-data research and enrichment

Combine community work with compatible external Swedish resources.

Goals:

- Research open Swedish speech datasets
- Review licenses carefully
- Identify resources that can legally and practically support the project

### Phase 4 - Future model exploration

Start model work only after the data foundation is in place.

Possible future questions:

- Should we fine-tune an existing open model?
- Should we target single-speaker or multi-speaker output first?
- How do we evaluate intelligibility and naturalness for Swedish?

This phase is **not clearly defined yet**, and that is intentional.

### Phase 5 - Community operations

Make the project sustainable as more people join.

Goals:

- Publish contribution guidelines
- Define release and moderation processes
- Keep the roadmap transparent
- Help new contributors onboard quickly

## First practical milestone

The first implementation milestone is:

**A mobile app plus backend/data pipeline for collecting, storing, reviewing, and exporting Swedish speech data.**

That means the first engineering work should focus on:

- contributor consent,
- recording flows,
- upload/storage,
- metadata,
- review/curation,
- and dataset export.

## Open questions for later

- What model architecture should be used?
- Should the first TTS target be a demo voice or a broader multi-speaker system?
- Which external datasets are legally compatible?
- How should the community vote on roadmap decisions?

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for the current contribution model.

## Near-term deliverables

- Structured documentation
- Mobile app scope definition
- Backend/data pipeline design
- Consent and data governance rules
- Initial open-data research

## Long-term vision

If the data pipeline, governance, and community foundation work well, this project can grow into a serious Swedish TTS initiative. The model work will come later, once the project has earned the right to do it well.
