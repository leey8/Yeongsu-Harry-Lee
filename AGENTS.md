# Agent conventions

This repository is a durable record of Harry Lee's professional and academic
work. Keep changes concise, evidence-based, and easy to audit.

## Working rules

- Read the relevant brief before changing an engagement.
- Put sourced inputs in `data/` and record provenance beside the input.
- Put the question, scope, and hypothesis before the work in `docs/briefs/`.
- Put recommendations and trade-offs after the work in `docs/decisions/`.
- Keep generated charts and analysis outputs in `analysis/figures/`.
- Do not add secrets, credentials, or unnecessary personal contact details.
- Preserve source attribution and distinguish facts from assumptions.
- Update `prompt-log.md` when an AI session materially changes the repository.

## AI interaction guidelines

### Core principles

- **Human ownership:** AI is a thought partner and productivity accelerator.
  The repository owner independently reviews and verifies all code, analysis,
  and written text before taking responsibility for committed work.
- **Academic integrity:** Core arguments, analytical conclusions, and
  reflections must reflect the repository owner's reasoning. AI must not be
  used to fabricate deliverables wholesale.

### Permitted use cases

- Workspace setup, including boilerplate, folder templates, and configuration
  files such as `.gitignore`
- Proofreading, editing, syntax fixes, and readability improvements
- Learning, debugging, concept explanations, and analytical-framework
  brainstorming

### Prohibited use cases

- Committing raw AI output without independent review or verification
- Sharing private API keys, credentials, or confidential personal data in AI
  prompts
- Delegating the primary thinking or drafting stages for course briefs and
  memos

### Transparency and logging

Document significant AI interactions, prompt iterations, and key
problem-solving sessions in `prompt-log.md`.

## Engagement structure

Each capability gets a directory under `capabilities/`. At minimum, include a
`README.md` describing the capability and a `spec.md` defining its scope,
inputs, outputs, and limitations.
