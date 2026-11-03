---
name: interrogate
description: "Use for \"interrogate\", \"adversarial review\", \"multi-model review\", \"challenge this\", \"stress test this code\", \"find blind spots\", or \"tear this apart\". Adversaries try to break a change or design, and only findings proven by a run survive."
---

# Interrogate

Adversaries try to break a change or a design. A finding survives only when a run proves it. Report the findings. Do not apply fixes.

## Frame

The scope is what the user names. Otherwise it is the branch diff from its merge base with the base branch, through the working tree. State the intent in your own words, from the user's message, the commits, and the PR description. Ask the user when the intent is unclear.

## Fan out

Interrogate runs its own panel. It does not need the `arena` skill, so a repository that vendors only `interrogate` can run it.

1. Pick one adversary per entry on the `interrogate reviewers` line in `jstack-models.md`. If the sheet or that line is missing, use the defaults in [Models](#models). Omit `model` for an `auto` or `inherit-parent` entry, so that adversary runs on the parent model. If the `Agent` tool rejects an entry, run that seat on its family's default and say so. Families go by model name, such as Opus, Fable, or Sonnet. With no family default, use the closest valid slug of the same family from the error message, or drop the seat and say so. If drops leave fewer than two adversaries, add one on a remaining adversary's model, or on the parent model when none remain.
2. Give each adversary its own git worktree at the scope's commit, and copy the scope's uncommitted changes into it. Without git, use `/tmp/interrogate-<slug>/adversary-<n>/`.
3. Spawn all adversaries in one message with `run_in_background: true` and `subagent_type: "general-purpose"`. Give each adversary the scope, the intent, its worktree path, and this brief:

> Break this change. Prove each finding with a run against the real code, such as a failing test, a repro script, or command output that shows the wrong behavior. For a design or plan, run a prototype or probe the current tree. Keep repros in your own worktree. Report each finding with a title, the exact command, the output it printed, and the output the intent requires. Leave out anything you did not run. That covers style, naming, structure, and risks that need a future change to matter. No findings is a valid result.

If an adversary returns nothing, continue without it and note the dropout.

## Verify

Rerun each finding's command yourself. Keep a finding when the output shows the failure and the failure breaks the intent. Drop the rest. Merge findings that share a root cause.

There is no judge, pick, or graft step. Adversaries find different failures, so the verdict keeps every verified finding, not one adversary's list.

## Output

- **Intent**: the restatement.
- **Findings**: for each verified finding, the command, its output, how it breaks the intent, and the adversaries that found it. Say "no verified findings" when none survive.
- **Dropped**: for each adversary, the count of dropped findings and why.

## Other runtimes

This skill is self-contained, so a repository that vendors only `interrogate` has everything it needs here.

- **Codex**: each adversary is a `spawn_agent` call. The `subagent_type` and `model` dispatch fields map to `spawn_agent`, which already runs concurrently, so `run_in_background` has no separate flag. Substitute your configured Codex models for the defaults and keep the panel model-diverse. If `spawn_agent` is unavailable (the `multi_agent` feature is off), run the adversaries as sequential passes and say so. A thread-limit error is a capacity error, not a rejected model, so stop spawning instead of falling back to another model.
- **GitHub Copilot**: each adversary is a `task` call. `subagent_type` becomes `agent_type`, and `model` comes from the `jstack-models.md` sheet. Keep the panel's vendors distinct (Claude, GPT, Gemini, Grok, and so on).
- On either runtime, if only one model family is reachable, vary reasoning effort across adversaries and note in the output that diversity was reduced.

## Models

Role defaults, stamped from `plugins/jstack/models.json` (edit there, rerun `tools/generate.mjs`). A matching role line in the `jstack-models.md` override sheet overrides each at runtime; `/setup-jstack` writes it and lists its path per runtime.

- interrogate reviewers: `opus`, `fable`, `sonnet`, `haiku`

## Reasoning effort

A role value in the override sheet may name a reasoning effort after its model, as in `opus @xhigh`. Levels on Claude Code: `low`, `medium`, `high`, `xhigh`, `max`. Which ones apply depends on the model. A value without `@` takes the sheet's `default effort` line, a level or `session`, and `session` when the sheet has no such line. On Claude Code, `session` sets no effort, so the dispatch is the usual one. On other runtimes, follow the platform mapping for `session`. Strip the suffix before reading the model: `inherit-parent` or `auto` still omits `model` at every level, and a model name is passed as `model`. On Claude Code, a level picks the effort agent from the `subagent_type` you would otherwise use. `jstack:jedi-agent` becomes `subagent_type: "jstack:jedi-agent-<level>"`. `general-purpose`, or no `subagent_type`, becomes `subagent_type: "jstack:effort-<level>"`. The effort agents set only `effort`, so the model you pass still decides the model. On Codex, pass the level as `spawn_agent`'s `reasoning_effort` and keep the usual instructions.
