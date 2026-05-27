# ADR 0001: Use Codex, OpenSpec, and Superpowers

## Status

Proposed

## Context

The organization needs repeatable behavior-change workflows that preserve
decisions outside chat history and keep agent execution disciplined.

## Decision

Use Codex App for agent-assisted development, OpenSpec for intended behavior,
Superpowers for execution discipline, and the OpenSpec Superpowers bridge to keep
brainstorming, plans, verification, and retrospectives under
`openspec/changes/<change-name>/`.

## Consequences

- Behavior changes have reviewable artifacts before implementation.
- Agents can use a consistent lifecycle across repositories.
- Developers can ask "What's next in the Superpowers/OpenSpec flow?" instead of
  guessing the next step.

## Revisit Trigger

Revisit if the organization adopts a different agent workflow or spec system.
