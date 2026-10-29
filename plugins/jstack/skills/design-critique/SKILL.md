---
name: design-critique
description: Critique a UI change before its PR is marked ready. Checks it against the approved mock and production, runs a screen critique plus a quick accessibility pass, fixes blockers in the branch, and writes the rest to the PR body. Repo specifics come from the repo's AGENTS.md. Method from Critiquito.
---

# Design critique

Name what the person using the screen will trip on, ranked by what it costs them. Lead with the verdict.

## Repo section first

Read the `Design critique` section of the repo's AGENTS.md. It names the UI paths, design-system docs, baseline, inputs (widths, themes, screenshot source, login), tracker, and any checklist. It wins over this skill. If it's missing, use the fallbacks below and say so in the verdict.

Skip the critique when the diff doesn't touch the repo's UI paths.

## Input

- Screenshot every screen the diff touches, plus each empty, loading, and error state it adds or changes, at the repo section's widths and themes.
- Fallback: 1280px and 390px, light theme, from a preview deploy if one exists, else local dev.
- Never critique a screen you haven't seen. Name what you couldn't reach.
- Never put credentials, demo passwords, or bypass tokens in a PR, a comment, or a screenshot. Never touch live payment modes.

## Baseline

1. The approved mock or board. Name drift. Don't re-argue decisions Jake approved.
2. Production, for screens that already exist. A new regression is at least worth fixing.

## Design system

Read the docs the repo section names before your own defaults. Flag off-token colors, spacing, radii, and type sizes, naming the right token. Flag a one-off component when an existing one does the job, naming it. Don't assume a framework.

## Modes

In `modes/`. Default: `screen.md` plus the quick pass in `accessibility.md`. Add others only on their trigger:

- `compare.md`: the PR has variants, or the before/after is the question.
- `flow.md`: the diff changes a multi-step path such as signup, checkout, or intake.
- `copy.md`: the diff mainly changes words: buttons, errors, empty states, onboarding.
- Full `accessibility.md`: the diff adds forms, modals, or custom controls, or a finding involves contrast, targets, or labels.

## Severity

- Blocker: the user can't finish the task or can't read the screen.
- Worth fixing: it costs time or trust, or breaks the design system.
- Minor: polish.

## Rules

- Quote what you see. Never invent a hex value, measurement, token, or component name. On a compressed image, estimate and say so.
- Judge the user's job, not taste. Every finding says what it costs the user.
- Cap at ten findings per screen. Say how many you cut.
- Never edit a Figma or design file.

## Output

1. Fix blockers in the branch, then re-screenshot and re-check them.
2. Write a `Design critique` section in the PR body: who uses the screen and why, a one-line verdict, then Fix first, Worth fixing, Minor, Working well, Could not judge.
3. Draft one follow-up per unfixed worth-fixing item for the repo's tracker. Put the drafts in the PR body when it moves to review. File them only after Jake says yes.
4. Keep a per-screen open-fix list only if the repo section asks for one, and only for screens with open blockers.
