# Agent Operating Guide

This repository is a document-first personal assistant workspace.

## Required Read Order

When an Agent enters this repository, read files in this order:

1. `agent/session-start.md`
2. `memory/index.md`
3. `reviews/index.md`
4. `okr/index.md`
5. `agent/workflows.md`

Then route to the relevant domain based on the user's request.

## Domain Boundaries

- `agent/`: how to work in this repository
- `memory/`: long-term personal context about the owner and ongoing matters
- `reviews/`: progress reviews and periodic check-ins
- `okr/`: quarterly planning and tracking
- `knowledge/`: reusable knowledge not tied to one specific personal context
- `inbox/`: raw captures before structuring

## Update Rules

- Prefer updating existing canonical files instead of scattering new notes.
- Put raw information into `inbox/` when classification is unclear.
- When a change affects goals, also inspect `okr/`.
- When a review produces decisions or stable facts, sync them back into `memory/`.
- Maintain `related` links in frontmatter whenever documents influence each other.

## Writing Rules

- Use Markdown with YAML frontmatter when the document is part of a managed domain.
- Keep directory names and YAML keys in English.
- Keep explanatory text mainly in Chinese unless an English label improves readability.
- Preserve template section names so later sessions can parse them consistently.

## Typical Tasks

- Capture a new fact: use `inbox/` or `templates/inbox-capture.md`
- Update long-term memory: use `templates/memory-entry.md`
- Run a review: use files in `reviews/templates/`
- Draft or update OKR: use files in `okr/templates/`
