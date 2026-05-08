# Data Governance

## Purpose

This document defines the principles that should guide how voice data is collected, reviewed, stored, and eventually used in this project.

## Core rule

No speech data should be collected or reused without clear consent and clear usage expectations.

## Topics this document covers

- consent
- privacy
- licensing
- moderation
- dataset quality
- future model-use expectations

## Consent principles

Contributors should be told clearly:

- what they are recording
- what metadata is collected
- how recordings are stored
- whether recordings may be published
- whether recordings may later be used for training or evaluation
- whether they can request removal

## Privacy principles

This project should minimize personal data collection.

The project should avoid collecting unnecessary personal identifiers and should separate contributor identity from dataset assets whenever possible.

## Licensing principles

Before any dataset is imported or released, the project should confirm:

- whether redistribution is allowed
- whether commercial use is allowed
- whether model training use is allowed
- whether attribution is required
- whether derivative data products are allowed

## Quality principles

Useful quality checks may include:

- clipped audio detection
- silence detection
- duplicate detection
- transcript completeness
- background noise review

## Moderation principles

The project should be able to reject or quarantine recordings that are:

- low quality
- abusive
- unsafe
- duplicated
- submitted without valid consent

## Future model use

Future model training or fine-tuning should only happen after:

- consent language is clear,
- licensing is reviewed,
- data quality is acceptable,
- and the project is operationally ready.

## Status

This is an initial governance document and should be refined before live data collection begins.
